---
tags:
  - 42_seoul
---
# LIBFT

**libft** 과제를 하면서 알아야 할 개념들과 유의해야할 점들에 대해 정리한다

---
# 개념
## 1. int 와 long

CPU(레지스터)에 따른 int와 long의 크기

|자료형|16bit|32bit|64bit|
|----|----|----|----|
|int|2byte|4byte|4byte|
|long|4byte|4byte|8byte|

---
## 2. size_t

이와 같이 CPU(레지스터)의 따라 자료형의 범위가 달라진다.
이러한 이식성(호환성) 문제를 해결하기 위해 size_t라는 표준 alias를 정의했다.
size_t는 각 운영체제의 맞는 비트의 부호없는 정수로 의도한 것을 보장받을 수 있다.

---
# 유의점
## ft_memchr

```c
void *ft_memchr(const void *str, int c, size_t n)
{
	size_t i;
	unsigned char *temp;

	i = 0;
	temp = (unsigned char *)str;
	while (i < n)
		{
			if (temp[i] == (unsigned char)c)
				return ((void *)(str + i));
			i++;
		}
	return (0);
}
```

`mem` 함수에선 `void *`으로 변환된 주소 값과 `int`형의 찾을 문자를 `unsigned char`으로 다시 변환해 각 메모리 값을 비교한다.

`unsigned char`은 모든 bit를 투명하게 볼 수 있는 특성을 제공한다. 다른 자료형들은 내부 비트의 일부를 값을 표현하기 위한 용도가 아닌 다른 용도로 사용될 수 있으나, `unsigned char`은 이러한 것들을 허용하지 않는다. 또한 `signed char`보다 `unsigned char`이 표현할 수 있는 값의 개수가 많다는 것도 유의할 점이다.

따라서, 임의의 메모리에 바이트 단위로 접근해 값을 다룰 때에는, 반드시 `unsigned char`을 이용해야 `full portability`를 얻을 수 있다.

---
## ft_memmove

```c
void *ft_memmove(void *dst, const void *src, size_t len)
{
	unsigned char *temp_dst;
	unsigned char *temp_src;
	
	temp_dst = (unsigned char *)dst;
	temp_src = (unsigned char *)src;
	if (!dst && !src)
		return (0);
	// dst의 주소 값이 src의 주소 값보다 작은 경우
	if (temp_dst <= temp_src)
	{
		while (len-- > 0)
			*temp_dst++ = *temp_src++;
	}
	// dst의 주소 값이 src의 주소 값보다 큰 경우
	else
	{
		while (len-- > 0)
			*(temp_dst + len) = *(temp_src + len);
	}
	return (dst);
}
```

`ft_memmove`와 `ft_memcpy`는 특정 메모리 주소에서 원하는 크기 만큼을 다른 곳으로 복사를 해준다는 같은 특성을 가진 함수이지만, 차이점을 가지고 있다.

바로, `ft_memmove`는 복사하려는 메모리와 받으려는 메모리가 겹쳤을 때, 즉 overlap 현상이 있는 경우도 방지해 옮겨준다는 점이다.

---
## ft_split

```c
char **free_all(char **temp)
{
	size_t i;
	
	i = 0;
	while (temp[i])
		free(temp[i++]);
	free(temp);
	return (0);
}

char **ft_split(char const *str, char c)
{
	int i;
	int count;
	char **result;
	
	if (!str)
		return (0);
	i = 0;
	count = count_words(str, c);
	result = (char **)malloc(sizeof(char *) * (count + 1));
	if (!result)
		return (0);
	while (i < count && *str)
	{
		while (*str == c)
			str++;
		if (*str == '\0')
			break ;
		result[i] = sperate_words(str, c);
		if (!result[i])
			return (free_all(result));
		str += ft_strlen(result[i++]);
	}
	result[i] = 0;
	return (result);
}
```

라피신 과정에서 미리 풀어본 함수이지만, 그때와는 약간의 차이점이 있다.
라피신 과정에서는 `malloc`중에 오류가 일어나더라도, 처리 과정없이 바로 리턴을 해주었다.

하지만 본과정에서는 `malloc`중에 오류가 일어나면, 그 전까지 동적할당을 해주었던 모든 메모리들을 모두 해제하고 함수를 리턴을 해주어야 한다. 

---