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


