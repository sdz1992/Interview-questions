# 数据结构与算法面试题

### 1.把二元查找树转变成排序的双向链表
###### 题目：输入一棵二元查找树，将该二元查找树转换成一个排序的双向链表。要求不能创建任何新的结点，只调整指针的指向。
        比如将二元查找树
                                            10
                                          /    \
                                        6       14
                                      /  \     /　 \
                                   　4     8  12 　  16
        转换成双向链表4=6=8=10=12=14=16。
>分析：本题是微软的面试题。很多与树相关的题目都是用递归的思路来解决，本题也不例外。下面我们用两种不同的递归思路来分析。
>>思路一：当我们到达某一结点准备调整以该结点为根结点的子树时，先调整其左子树将左子树转换成一个排好序的左子链表，再调整其右子树转换右子链表。最近链接左子链表的最右结点（左子树的最大结点）、当前结点和右子链表的最左结点（右子树的最小结点）。从树的根结点开始递归调整所有结点。
>>>思路二：我们可以中序遍历整棵树。按照这个方式遍历树，比较小的结点先访问。如果我们每访问一个结点，假设之前访问过的结点已经调整成一个排序双向链表，我们再把调整当前结点的指针将其链接到链表的末尾。当所有结点都访问过之后，整棵树也就转换成一个排序双向链表了。

- 参考代码：

        首先我们定义二元查找树结点的数据结构如下：
        struct BSTreeNode // a node in the binary search tree
        {
            int          m_nValue; // value of node
            BSTreeNode  *m_pLeft;  // left child of node
            BSTreeNode  *m_pRight; // right child of node
        };
    
- 思路一对应的代码：
        
        ///////////////////////////////////////////////////////////////////////
        // Covert a sub binary-search-tree into a sorted double-linked list
        // Input: pNode - the head of the sub tree
        //        asRight - whether pNode is the right child of its parent
        // Output: if asRight is true, return the least node in the sub-tree
        //         else return the greatest node in the sub-tree
        ///////////////////////////////////////////////////////////////////////
        BSTreeNode* ConvertNode(BSTreeNode* pNode, bool asRight)
        {
        	if(!pNode)
		    return NULL;

	        BSTreeNode *pLeft = NULL;
	        BSTreeNode *pRight = NULL;

        	// Convert the left sub-tree
        	if(pNode->m_pLeft)
        		pLeft = ConvertNode(pNode->m_pLeft, false);

        	// Connect the greatest node in the left sub-tree to the current node
        	if(pLeft)
        	{
        		pLeft->m_pRight = pNode;
        		pNode->m_pLeft = pLeft;
        	}

        	// Convert the right sub-tree
        	if(pNode->m_pRight)
        		pRight = ConvertNode(pNode->m_pRight, true);
        
        	// Connect the least node in the right sub-tree to the current node
        	if(pRight)
        	{
        		pNode->m_pRight = pRight;
        		pRight->m_pLeft = pNode;
        	}
        
        	BSTreeNode *pTemp = pNode;
        
        	// If the current node is the right child of its parent, 
        	// return the least node in the tree whose root is the current node
        	if(asRight)
        	{
        		while(pTemp->m_pLeft)
        			pTemp = pTemp->m_pLeft;
        	}
        	// If the current node is the left child of its parent, 
        	// return the greatest node in the tree whose root is the current node
        	else
        	{
        		while(pTemp->m_pRight)
        			pTemp = pTemp->m_pRight;
        	}
         
        	return pTemp;
        }

        ///////////////////////////////////////////////////////////////////////
        // Covert a binary search tree into a sorted double-linked list
        // Input: the head of tree
        // Output: the head of sorted double-linked list
        ///////////////////////////////////////////////////////////////////////
        BSTreeNode* Convert(BSTreeNode* pHeadOfTree)
        {
        	// As we want to return the head of the sorted double-linked list,
        	// we set the second parameter to be true
        	return ConvertNode(pHeadOfTree, true);
        }
        
- 思路二对应的代码：
        
        ///////////////////////////////////////////////////////////////////////
        // Covert a sub binary-search-tree into a sorted double-linked list
        // Input: pNode -           the head of the sub tree
        //        pLastNodeInList - the tail of the double-linked list
        ///////////////////////////////////////////////////////////////////////
        void ConvertNode(BSTreeNode* pNode, BSTreeNode*& pLastNodeInList)
        {
        	if(pNode == NULL)
        		return;
        
        	BSTreeNode *pCurrent = pNode;
        
        	// Convert the left sub-tree
        	if (pCurrent->m_pLeft != NULL)
        		ConvertNode(pCurrent->m_pLeft, pLastNodeInList);
        
        	// Put the current node into the double-linked list
        	pCurrent->m_pLeft = pLastNodeInList; 
        	if(pLastNodeInList != NULL)
        		pLastNodeInList->m_pRight = pCurrent;
        
        	pLastNodeInList = pCurrent;
        
        	// Convert the right sub-tree
        	if (pCurrent->m_pRight != NULL)
        		ConvertNode(pCurrent->m_pRight, pLastNodeInList);
        }
        
        ///////////////////////////////////////////////////////////////////////
        // Covert a binary search tree into a sorted double-linked list
        // Input: pHeadOfTree - the head of tree
        // Output: the head of sorted double-linked list
        ///////////////////////////////////////////////////////////////////////
        BSTreeNode* Convert_Solution1(BSTreeNode* pHeadOfTree)
        {
        	BSTreeNode *pLastNodeInList = NULL;
        	ConvertNode(pHeadOfTree, pLastNodeInList);
        
        	// Get the head of the double-linked list
        	BSTreeNode *pHeadOfList = pLastNodeInList;
        	while(pHeadOfList && pHeadOfList->m_pLeft)
        		pHeadOfList = pHeadOfList->m_pLeft;
        
        	return pHeadOfList;
        }

### 2.设计包含min函数的栈
###### 题目：定义栈的数据结构，要求添加一个min函数，能够得到栈的最小元素。要求函数min、push以及pop的时间复杂度都是O(1)。 
>分析：这是去年google的一道面试题。我看到这道题目时，第一反应就是每次push一个新元素时，将栈里所有逆序元素排序。这样栈顶元素将是最小元素。但由于不能保证最后push进栈的元素最先出栈，这种思路设计的数据结构已经不是一个栈了。
>>在栈里添加一个成员变量存放最小元素（或最小元素的位置）。每次push一个新元素进栈的时候，如果该元素比当前的最小元素还要小，则更新最小元素。
>>>乍一看这样思路挺好的。但仔细一想，该思路存在一个重要的问题：如果当前最小元素被pop出去，如何才能得到下一个最小元素？
>>>>因此仅仅只添加一个成员变量存放最小元素（或最小元素的位置）是不够的。我们需要一个辅助栈。每次push一个新元素的时候，同时将最小元素（或最小元素的位置。考虑到栈元素的类型可能是复杂的数据结构，用最小元素的位置将能减少空间消耗）push到辅助栈中；每次pop一个元素出栈的时候，同时pop辅助栈。

- 参考代码：

        #include <deque>
        #include <assert.h>
        
        template <typename T> class CStackWithMin
        {
        public:
        	CStackWithMin(void) {}
        	virtual ~CStackWithMin(void) {}
        
        	T& top(void);
        	const T& top(void) const;
        
        	void push(const T& value);
        	void pop(void);
        
        	const T& min(void) const;
        
        private:
        	T>m_data;// theelements of stack
        	size_t>m_minIndex;// the indicesof minimum elements
        };
        
        // get the last element of mutable stack
        template <typename T> T& CStackWithMin<T>::top()
        {
        	return m_data.back();
        }
        
        // get the last element of non-mutable stack
        template <typename T> const T& CStackWithMin<T>::top() const
        {
        	return m_data.back();
        }
        
        // insert an elment at the end of stack
        template <typename T> void CStackWithMin<T>::push(const T& value)
        {
        	// append the data into the end of m_data
        	m_data.push_back(value);
        
        	// set the index of minimum elment in m_data at the end of m_minIndex
        	if(m_minIndex.size() == 0)
        		m_minIndex.push_back(0);
        	else
        	{
        		if(value < m_data[m_minIndex.back()])
        			m_minIndex.push_back(m_data.size() - 1);
        		else
        			m_minIndex.push_back(m_minIndex.back());
        	}
        }
        
        // erease the element at the end of stack
        template <typename T> void CStackWithMin<T>::pop()
        {
        	// pop m_data
        	m_data.pop_back();
        
        	// pop m_minIndex
        	m_minIndex.pop_back();
        }
        
        // get the minimum element of stack
        template <typename T> const T& CStackWithMin<T>::min() const
        {
        	assert(m_data.size() > 0);
        	assert(m_minIndex.size() > 0);
        
        	return m_data[m_minIndex.back()];
        }
        
- 举个例子演示上述代码的运行过程：

步骤|数据栈|辅助栈|最小值
----|----|----|----
1.push3|3|0|3
2.push4|3,4|0,0|3
3.push2|3,4,2|0,0,2|2
4.push1|3,4,2,1|0,0,2,3|1
5.pop|3,4,2|0,0,2|2
6.pop|3,4|0,0|3
7.push0|3,4,0|0,0,2|0
>讨论：如果思路正确，编写上述代码不是一件很难的事情。但如果能注意一些细节无疑         能在面试中加分。

        比如我在上面的代码中做了如下的工作：
        用模板类实现。如果别人的元素类型只是int类型，模板将能给面试官带来好印象；
        两个版本的top函数。在很多类中，都需要提供const和非const版本的成员访问函数；
        min函数中assert。把代码写的尽量安全是每个软件公司对程序员的要求；
        添加一些注释。注释既能提高代码的可读性，又能增加代码量，何乐而不为？
        总之，在面试时如果时间允许，尽量把代码写的漂亮一些。
        说不定代码中的几个小亮点就能让自己轻松拿到心仪的Offer。

