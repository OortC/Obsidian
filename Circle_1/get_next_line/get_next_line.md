---
tags: 42_seoul
---
# Get Next Line

이 과제는 `fd`를 받아, `read`를 통해 파일에서 읽어온 내용을 개행을 기준으로 나누어 출력하는 것이 목표이다.
`Mandatory Part`에선 한 가지의 파일만 읽고,` Bonus`에선 동시에 여러가지 파일들을 읽을 수 있도록 한다. 
과제를 진행하면서 필요한 알아야 할 개념과 개인적으로 느낀 유의점에 대해 설명한다.

---
## 1. File Descriptor (fd)

UNIX 시스템에서는 존재하는 모든 객체(정규 파일, 디렉토리, 소켓, 파이프 등)들은 파일로써 관리된다. 또한 시스템에서 프로세스가 위와 같은 파일들에 접근할 때는 File Descriptor를 이용하여 접근한다.

응용 프로세스가 파일을 열거나 생성하면, 3 이상의 정수로 된 fd값이 할당된다. 이 fd값은 모든 파일 동작(read, write, fcntl, close)등의 동작에서 그 파일을 가리키는 용도로 사용된다.  

자세한 내용은 [[File Descriptor]]에서 서술하도록 한다.

---
## 2. Static

개행을 기준으로 출력값을 나누어야 하기 때문에, 개행 뒤에 있는 문자열들을 나중을 위해 저장할 필요가 있다.
이때 필요한 것이 `static`이라는 변수 형태이다. 

전역 변수는 '전역'이라는 말 그대로 모든 곳에서 접근이 가능하며 프로그램 종료 전까지 메모리가 소멸되지 않는 변수이다. 하지만 잘못 사용하거나 남용할 경우 유지보수가 어려워 진다.
이를 부분적으로 보완한 변수 형태가 정적(static) 변수이다. 

자세한 내용은 [[Static]]에서 서술한도록 한다.

---
## 유의점
### 1) 메모리 관리 (Leak)

이번 과제에선 동적할당한 해준 메모리들을 누수가 나지 않도록 적절하게 해제하는 것을 요구한다.
과제 중, 대부분 메모리 적절하게 해제를 해주었지만 오랜 기간동안 누수에 어려움을 겪은 부분은 두 부분이 있다.
이 두 부분에 관해서만 서술해보도록 한다.

```c
char *read_buff(int fd, char *save)
{
	int m_size;
	int r_size;
	char buff[BUFFER_SIZE + 1];
	  
	m_size = ft_strlen(save);
	while (!(ft_strchr(save, '\n')))
	{
		r_size = read(fd, buff, BUFFER_SIZE);
		if (r_size < 0)
			return (take_free(save));
		if (r_size == 0)
			break ;
		buff[r_size] = '\0';
		if (!save)
			save = ft_strdup(buff, &m_size);
		else
			save = ft_strjoin(save, buff, &m_size);
		if (!save)
			return (0);
	}
	return (save);
}
  
char *get_next_line(int fd)
	{
	char *result;
	static char *save;
	  
	if (fd < 0 || BUFFER_SIZE <= 0 || fd > OPEN_MAX)
		return (0);
	save = read_buff(fd, save);
	if (!save)
		return (0);
	result = take_line(save);
	if (!result)
	{
		save = take_free(save);
		return (0);
	}
	save = take_save(save);
	return (result);
	}
```

첫 번째로는, `read`함수 사용 중 에러가 일어나 `return`값이 -1인 경우, 지금까지 `static` 변수인 `save`에 할당해주었던 모든 메모리를 해제해야 한다.

두 번째로는, `take_line`에서 동적할당이 제대로 이루어지지 않아 `NULL`이 `return`된 경우에도, `save`의 모든 메모리를 해제해주고 0을 `return`함으로써 함수를 끝내야 한다.

---
### 2) Dangling Pointer

댕글링 포인터란 해제된 포인터가 여전히 해제된 메모리 영역을 가리키고 있는 상태를 뜻한다.
```c
int main(void)
{
	char *temp;
  
	temp = (char *)malloc(sizeof(char) * 4);
	printf("해제 전 주소 값 : %p\n", temp);
	
	free(temp);
	printf("해제 후 주소 값 : %p", temp);
	
	return (0);
}
```

이런 식으로 동적할당을 한 뒤, 해제까지 한 메모리의 주소 값을 출력해보면

	해제 전 주소 값 : 0x7f9897405820
	해제 후 주소 값 : 0x7f9897405820

이와 같이, 해제 전과 후가 같은 주소를 가리키는 걸 볼 수 있다.
이렇게 댕글링 포인터가 생긴 경우, 아래와 같은 다양한 문제를 야기시킨다.

- 메모리 접근 시 예측 불가능한 동작을 일으킬 수 있음
- 메모리 접근 불가 시 `Segmentation fault`를 발생시킴
- 잠재적인 보안 위험 가능성

따라서, 메모리를 동적할당 한 후, 해제할 때에는 아래와 같이 포인터를 적절하게 `NULL`로 설정해야한다.

```c
temp = (char *)malloc(sizeof(char) * 4);

free(temp);
temp = NULL;
```

---
### 3) Timeout

과제를 진행하면서 가장 골머리를 앓은 부분이 바로 `timeout`을 해결하는 것이였다.
여러 방면으로 해결하려고 노력한 결과, `malloc`의 호출 횟수를 줄이는 것이 좋은 방법이였다.

`malloc`을 할 때마다, 크기에 딱 맞는 사이즈를 동적할당을 하는 것이 아닌 여유 공간까지 추가로 할당하여, 할당한 메모리가 부족하지 않을 땐 `ft_strlcat`으로 붙이는 것이 `malloc`의 호출 횟수를 줄이는 가장 효과적인 방법이였다.

이와 관련한 `read_buff`, `ft_strdup`, `ft_strjoin`의 코드를 봐보자.
```c
char *read_buff(int fd, char *save)
{
	int m_size;
	int r_size;
	char buff[BUFFER_SIZE + 1];
	  
	m_size = ft_strlen(save);
	while (!(ft_strchr(save, '\n')))
	{
		r_size = read(fd, buff, BUFFER_SIZE);
		if (r_size < 0)
			return (take_free(save));
		if (r_size == 0)
			break ;
		buff[r_size] = '\0';
		if (!save)
			save = ft_strdup(buff, &m_size);
		else
			save = ft_strjoin(save, buff, &m_size);
		if (!save)
			return (0);
	}
	return (save);
}

char *ft_strdup(char *buff, int *m_size)
{
	int size;
	char *result;
	  
	size = ft_strlen(buff) + 1;
	result = (char *)malloc(sizeof(char) * (BUFFER_SIZE * 2));
	if (!result)
		return (take_free(result));
	*m_size = BUFFER_SIZE * 2;
	result[0] = '\0';
	ft_strlcat(result, buff, size);
	return (result);
}
  
char *ft_strjoin(char *save, char const *buff, int *m_size)
{
	int i;
	int size;
	char *result;
	  
	result = NULL;
	if (save && buff)
	{
		size = ft_strlen(save) + ft_strlen(buff) + 1;
		if (*m_size >= size)
		{
			ft_strlcat(save, buff, size);
			return (save);
		}
		*m_size = (size / BUFFER_SIZE + 1) * BUFFER_SIZE * 2;
		result = (char *)malloc(sizeof(char) * (*m_size));
		if (!result)
			return (take_free(save));
		i = -1;
		while (++i < (int)ft_strlen(save))
			result[i] = save[i];
		result[i] = '\0';
		ft_strlcat(result, buff, size);
	}
	take_free(save);
	return (result);
}
```

`read_buff`에서 버퍼를 읽고 `save`에 저장하게 되는데, 이 과정에서 두가지 분기로 나뉜다.

첫 번째로는 save가 NULL일 경우, 이 경우에선 ft_strdup에서 따로 동적할당을 진행해주게 되는데, 일반적인 dup은 들어온 매개변수의 길이 만큼 동적할당을 해주게 된다. 하지만 나의 경우, 여유 메모리 공간을 확보하기 위해 BUFFER_SIZE의 두 배만큼 추가로 동적할당을 해주어 확보하여 buff를 복사하여 붙혀 넣는다.

두 번째로는 save에 어떤 문자라도 들어있는 경우이다. 이 경우에는 read_buff에서 선언한 m_size가 활용되는데, m_size는 현재 save에 얼마만큼의 메모리 공간이 할당 되어있는지를 뜻한다. 이 메모리 공간은 현재 save의 문자열 길이와 추가하려는 buff의 문자열의 길이의 합과 비교되어 메모리 공간이 부족한지 여유로운 지 체크하는 역활로써 쓰인다.

메모리 공간이 여유로울 경우, ft_strlcat을 이용하여 추가적인 할당없이 붙혀넣어 시간복잡도를 낮추게 된다. 하지만 메모리 공간이 부족할 경우, m_size를 BUFFER_SIZE과 함께 계산해 전 메모리 공간의 2배씩을 할당해주어 여유 공간을 추가적으로 할당한다.

---