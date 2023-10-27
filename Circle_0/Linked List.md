---
tags:
  - 42_seoul
  - 자료구조
---
# Linked List
---
## Array와 Linked List

![[Array_L_List.png]]
- ## Array

연속된 메모리 공간에 데이터들을 저장하는 자료구조

인덱스를 이용하여 각 배열에 접근하여 데이터를 쉽게 탐색 가능
배열 중간에 데이터를 추가 및 삭제의 경우, 배열을 밀거나 당겨야 함 
<br>
- ## Linked List

여러 개의 노드를 순차적으로 연결하여 데이터를 저장하는 자료구조
배열과는 다르게 노드들의 주소들은 흩어져 있으며, 각 노드가 다음 노드의 주소값을 저장함으로써 연결함

데이터 탐색 시, Head 노드부터 순차적으로 탐색해야함
데이터 삽입 및 제거 시 앞/뒤 노드의 주소값만 변경하면 됨

---
## 구조

```c
typedef struct s_list
{
	void *content;
	struct s_list *next;
} t_list;
```

---
## 주요 함수

### ft_lstnew

```c
t_list *ft_lstnew(void *content)
{
	t_list *node;
	
	node = (t_list *)malloc(sizeof(t_list));
	if (!node)
		return (0);
	node->content = content;
	node->next = NULL;
	return (node);
}
```

---
### ft_lstadd_front

```c
void ft_lstadd_front(t_list **lst, t_list *new)
{
	new->next = *lst;
	*lst = new;
}
```

---
### ft_lstadd_back

```c
void ft_lstadd_back(t_list **lst, t_list *new)
{
	if (!(*lst) && new)
	{
		*lst = new;
		return ;
	}
	else if (!lst || !new)
		return ;
	ft_lstlast(*lst)->next = new;
}
```

---
### ft_lstdelone

```c
void ft_lstdelone(t_list *lst, void (*del)(void *))
{
	if (!lst || !del)
		return ;
	del(lst->content);
	free(lst);
}
```

---
### ft_lstclear

```c
void ft_lstclear(t_list **lst, void (*del)(void *))
{
	t_list *temp;
	  
	if (!lst || !del)
		return ;
	while (*lst)
	{
		temp = (*lst)->next;
		del((*lst)->content);
		free(*lst);
		*lst = temp;
	}
}
```