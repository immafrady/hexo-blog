---
title: 203.移除链表元素  
date: 2023-01-24 17:46:45
tags:
- Leetcode
- Go
categories:
- Coding
---

[203.移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

> 给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 新的头节点 。

这一题是基础的链表操作题～

#### 1. 最基础的做法

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeElements(head *ListNode, val int) *ListNode {
	for head != nil && head.Val == val {
		head = head.Next
	}
	if head == nil {
		return nil
	}

	curr, prev := head.Next, head
	for curr != nil {
		if curr.Val == val {
			prev.Next = curr.Next
			curr = prev.Next
		} else {
			prev = prev.Next
			curr = curr.Next
		}
	}
	return head
}
```
首先想到的是，特殊处理头部，但是很不优雅

#### 2. 把头部合并处理

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeElements(head *ListNode, val int) *ListNode {

	dummy := &ListNode{Next: head}
	for cur := dummy; cur.Next != nil; {
		if cur.Next.Val == val {
			cur.Next = cur.Next.Next
		} else {
			cur = cur.Next
		}
	}

	return dummy.Next
}
```
在头部之前插入一个起点节点，这样包含head的全部节点，都可以使用相同的逻辑进行处理了
