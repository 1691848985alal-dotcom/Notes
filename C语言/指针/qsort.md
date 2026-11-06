---
tags: [C语言, 指针, qsort]
---

# 指针（实战）：qsort 使用与通用模拟实现

> 涵盖：qsort 的使用、比较函数写法，以及基于 `void*` 的通用冒泡排序模拟实现。
> 指针总览见 [[指针]]；前一篇见 [[指针/函数指针]]。

## 十五、qsort 使用
```c
void qsort(void *base, size_t nmemb, size_t size,
           int (*compar)(const void *, const void *));
```
- `base` 待排序首地址；`nmemb` 元素个数；`size` 单元素字节；`compar` 比较函数指针。排序直接作用于原数组。
- 比较函数：返回**负数** → p1 在前；**正数** → p2 在前；**0** → 相等。
- int 升序：`return *(int*)p1 - *(int*)p2;`；降序反过来写。
- ⚠️ 浮点/结构体**不要直接相减**（小数被截断成 0），用分支返回 1 / -1 / 0。

```c
int int_cmp(const void* p1, const void* p2) {
    return *(int*)p1 - *(int*)p2;
}
int arr[] = {1,3,5,7,9,2,4,6,8,0};
qsort(arr, sizeof(arr)/sizeof(arr[0]), sizeof(int), int_cmp);
```

## 十六、qsort 模拟实现（重点）
通用三要素：`void* base`（不假设类型）+ `size`（每个元素几字节）+ 比较函数指针（比较交给使用者）。
- `void*` 能接收任何地址，但**不能直接解引用/加减**，需先强转。
- 定位第 j 个元素：`(char*)base + j*size`（char 恰好 1 字节）。
- 交换任意类型：逐字节交换 `size` 次。

```c
void _swap(void* p1, void* p2, int size) {
    for (int i = 0; i < size; i++) {
        char tmp = *((char*)p1 + i);
        *((char*)p1 + i) = *((char*)p2 + i);
        *((char*)p2 + i) = tmp;
    }
}
void bubble(void* base, int count, int size,
            int (*cmp)(const void*, const void*)) {
    for (int i = 0; i < count - 1; i++)
        for (int j = 0; j < count - i - 1; j++)
            if (cmp((char*)base + j*size, (char*)base + (j+1)*size) > 0)
                _swap((char*)base + j*size, (char*)base + (j+1)*size, size);
}
```

---
⬆️ 返回 [[指针]] ｜ 上一篇：[[指针/函数指针]]
