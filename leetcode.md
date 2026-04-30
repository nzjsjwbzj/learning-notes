# day1两数相加

[2. 两数相加 - 力扣（LeetCode）](https://leetcode.cn/problems/add-two-numbers/description/)



```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2) {
    struct ListNode *dummy=(struct ListNode*)malloc(sizeof(struct ListNode));
    dummy->val=0;
    dummy->next=NULL;

    struct ListNode *current =dummy;
    int carry=0;

    while(l1!=NULL||l2!=NULL||carry!=0)
    {
        int val1=(l1!=NULL)?l1->val:0;
        int val2=(l2!=NULL)?l2->val:0;

        int sum =val1+val2+carry;
        carry=sum/10;

      struct ListNode *newnode=(struct ListNode*)malloc(sizeof(struct ListNode));

newnode->val=sum%10;
newnode->next=NULL;
current->next=newnode;
current=newnode;

if(l1!=NULL)    l1=l1->next;
if(l2!=NULL)    l2=l2->next;



    }
    struct ListNode *result=dummy->next;
    free(dummy);

    return result;

}
```

## 虚拟头节点

假设你要构建一个结果链表，初始时链表为空，你有一个指针 `current` 指向链表的尾部（便于添加新节点）。

开始时，链表为空，`current` 应该指向哪里？通常有两种做法：

1. 单独处理第一个节点：先创建第一个节点，将其作为头节点，再让 `current` 指向它。
2. 让 `current` 初始为 `NULL`，然后在每次添加节点时判断：如果 `current == NULL`，则这是第一个节点，需设置头节点；否则通过 `current->next` 添加。

无论哪种方式，都需要在循环内部做一次条件判断，代码显得冗余且容易出错。

```c
struct ListNode* head = NULL;
struct ListNode* current = NULL;
while (...) {
    // ... 计算 newNode
    if (head == NULL) {
        head = newNode;
        current = newNode;
    } else {
        current->next = newNode;
        current = newNode;
    }
}
```

## 使用虚拟头节点

```c
struct ListNode* dummy = malloc(...);
struct ListNode* current = dummy;
while (...) {
    // ... 计算 newNode
    current->next = newNode;
    current = newNode;
}
return dummy->next;
```

- 虚拟头节点本身是动态分配的，记得在使用后释放（`free(dummy)`），避免内存泄漏。
- 虚拟头节点的 `val` 值无意义，通常设为0。

虚拟头节点是一个极其实用的小技巧，能有效降低链表构建代码的复杂度

# day2最长无重复子串

[3. 无重复字符的最长子串 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)



```c
int lengthOfLongestSubstring(char* s) {
    // 哈希表记录每个字符最近一次出现的位置
    // 因为字符范围是ASCII 0-127，用128大小的数组即可
    int lastIndex[128];
    // 初始化所有字符的最近位置为 -1，表示从未出现
    for (int i = 0; i < 128; i++) {
        lastIndex[i] = -1;
    }

    int maxLen = 0;          // 最长子串长度
    int left = 0;            // 滑动窗口左边界

    // 遍历字符串，right 为右边界
    for (int right = 0; s[right] != '\0'; right++) {
        char c = s[right];
        // 如果当前字符在窗口内出现过（即上次出现位置 >= left）
        if (lastIndex[c] >= left) {
            // 将左边界移动到上次出现位置的下一个位置
            left = lastIndex[c] + 1;
        }
        // 更新当前字符的出现位置
        lastIndex[c] = right;
        // 计算当前窗口长度，并更新最大值
        int curLen = right - left + 1;
        if (curLen > maxLen) {
            maxLen = curLen;
        }
    }
    return maxLen;
}
```



## 哈希表

本质就是个数组，只不过下标和值是特殊处理过的

哈希表通常由两部分组成：

1. **数组**：一段连续的内存空间，用来存储数据。
2. **哈希函数**：将键转换为数组下标的函数。

**举个例子**：我们想存储一些人的年龄，键是姓名，值是年龄。

- 哈希函数：`index = (姓名的长度) % 10`（假设数组大小是10）。
- 存储 `("Alice", 25)`：`"Alice"` 长度是5，`5 % 10 = 5`，所以把 `25` 存在数组下标5的位置。
- 查询 `"Alice"` 的年龄：同样计算 `5 % 10`，直接取出下标5的值即可，非常快

## 滑动窗口

左右两个指针，不断右边移动指向新元素，来一个元素先通过哈希表查找(我还以为是遍历查找），看看窗口里面有没有重复的，没有的话就进来，有的话就移动左指针