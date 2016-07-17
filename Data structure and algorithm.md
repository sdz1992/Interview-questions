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

>讨论：如果思路正确，编写上述代码不是一件很难的事情。但如果能注意一些细节无疑能在面试中加分。

        比如我在上面的代码中做了如下的工作：
        用模板类实现。如果别人的元素类型只是int类型，模板将能给面试官带来好印象；
        两个版本的top函数。在很多类中，都需要提供const和非const版本的成员访问函数；
        min函数中assert。把代码写的尽量安全是每个软件公司对程序员的要求；
        添加一些注释。注释既能提高代码的可读性，又能增加代码量，何乐而不为？
        总之，在面试时如果时间允许，尽量把代码写的漂亮一些。
        说不定代码中的几个小亮点就能让自己轻松拿到心仪的Offer。


### 3.求子数组的最大和
###### 题目：输入一个整形数组，数组里有正数也有负数。数组中连续的一个或多个整数组成一个子数组，每个子数组都有一个和。求所有子数组的和的最大值。要求时间复杂度为O(n)。
        例如输入的数组为1, -2, 3, 10, -4, 7, 2, -5，和最大的子数组为3, 10, -4, 7, 2，因此输出为该子数组的和18。
>分析：本题最初为2005年浙江大学计算机系的考研题的最后一道程序设计题，在2006年里包括google在内的很多知名公司都把本题当作面试题。由于本题在网络中广为流传，本题也顺利成为2006年程序员面试题中经典中的经典。
>>如果不考虑时间复杂度，我们可以枚举出所有子数组并求出他们的和。不过非常遗憾的是，由于长度为n的数组有O(n2)个子数组；而且求一个长度为n的数组的和的时间复杂度为O(n)。因此这种思路的时间是O(n3)。
>>>很容易理解，当我们加上一个正数时，和会增加；当我们加上一个负数时，和会减少。如果当前得到的和是个负数，那么这个和在接下来的累加中应该抛弃并重新清零，不然的话这个负数将会减少接下来的和。基于这样的思路，我们可以写出如下代码。

- 参考代码：

        /////////////////////////////////////////////////////////////////////////////
        // Find the greatest sum of all sub-arrays
        // Return value: if the input is valid, return true, otherwise return false
        /////////////////////////////////////////////////////////////////////////////
        bool FindGreatestSumOfSubArray
        (
        	int *pData,           // an array
        	unsigned int nLength, // the length of array
        	int &nGreatestSum     // the greatest sum of all sub-arrays
        )
        {
        	// if the input is invalid, return false
        	if((pData == NULL) || (nLength == 0))
        		return false;
        
        	int nCurSum = nGreatestSum = 0;
        	for(unsigned int i = 0; i < nLength; ++i)
        	{
        		nCurSum += pData[i];
        
        		// if the current sum is negative, discard it
        		if(nCurSum < 0)
        			nCurSum = 0;
        
        		// if a greater sum is found, update the greatest sum
        		if(nCurSum > nGreatestSum)
        			nGreatestSum = nCurSum;
        	}
         
        	// if all data are negative, find the greatest element in the array
        	if(nGreatestSum == 0)
        	{
        		nGreatestSum = pData[0];
        		for(unsigned int i = 1; i < nLength; ++i)
        		{
        			if(pData[i] > nGreatestSum)
        				nGreatestSum = pData[i];
        		}
        	}
        
        	return true;
        } 
        
>讨论：上述代码中有两点值得和大家讨论一下：
>>函数的返回值不是子数组和的最大值，而是一个判断输入是否有效的标志。如果函数返回值的是子数组和的最大值，那么当输入一个空指针是应该返回什么呢？返回0？那这个函数的用户怎么区分输入无效和子数组和的最大值刚好是0这两中情况呢？基于这个考虑，本人认为把子数组和的最大值以引用的方式放到参数列表中，同时让函数返回一个函数是否正常执行的标志。
>>>输入有一类特殊情况需要特殊处理。当输入数组中所有整数都是负数时，子数组和的最大值就是数组中的最大元素。

### 4.在二元树中找出和为某一值的所有路径   
###### 题目：输入一个整数和一棵二元树。从树的根结点开始往下访问一直到叶结点所经过的所有结点形成一条路径。打印出和与输入整数相等的所有路径。
        例如输入整数22和如下二元树
                                            10
                                           /   \
                                          5     12
                                        /   \   
                                     　4     7  
        则打印出两条路径：10, 12和10, 5, 7。
        二元树结点的数据结构定义为：
        struct BinaryTreeNode // a node in the binary tree
        {
        	int              m_nValue; // value of node
        	BinaryTreeNode  *m_pLeft;  // left child of node
        	BinaryTreeNode  *m_pRight; // right child of node
        };
>分析：这是百度的一道笔试题，考查对树这种基本数据结构以及递归函数的理解。
当访问到某一结点时，把该结点添加到路径上，并累加当前结点的值。如果当前结点为叶结点并且当前路径的和刚好等于输入的整数，则当前的路径符合要求，我们把它打印出来。如果当前结点不是叶结点，则继续访问它的子结点。当前结点访问结束后，递归函数将自动回到父结点。因此我们在函数退出之前要在路径上删除当前结点并减去当前结点的值，以确保返回父结点时路径刚好是根结点到父结点的路径。我们不难看出保存路径的数据结构实际上是一个栈结构，因为路径要与递归调用状态一致，而递归调用本质就是一个压栈和出栈的过程。

- 参考代码：

        ///////////////////////////////////////////////////////////////////////
        // Find paths whose sum equal to expected sum
        ///////////////////////////////////////////////////////////////////////
        void FindPath
        (
        	BinaryTreeNode*   pTreeNode,    // a node of binary tree
        	int               expectedSum,  // the expected sum
        	std::vector<int>&path,        // a pathfrom root to current node
        	int&              currentSum    // the sum of path
        )
        {
        	if(!pTreeNode)
        		return;
        
        	currentSum += pTreeNode->m_nValue;
        	path.push_back(pTreeNode->m_nValue);
        
        	// if the node is a leaf, and the sum is same as pre-defined, 
        	// the path is what we want. print the path
        	bool isLeaf = (!pTreeNode->m_pLeft && !pTreeNode->m_pRight);
        	if(currentSum == expectedSum && isLeaf)
        	{		
        std::vector<int>::iterator iter =path.begin();
        for(; iter != path.end(); ++ iter)
        			std::cout<<*iter<<'\t';
        		std::cout<<std::endl;
        	}
        
        	// if the node is not a leaf, goto its children
        	if(pTreeNode->m_pLeft)
        		FindPath(pTreeNode->m_pLeft, expectedSum, path, currentSum);
        	if(pTreeNode->m_pRight)
        		FindPath(pTreeNode->m_pRight, expectedSum, path, currentSum);
        
        	// when we finish visiting a node and return to its parent node,
        	// we should delete this node from the path and 
        	// minus the node's value from the current sum
        	currentSum -= pTreeNode->m_nValue;	//!!I think here is no use
        	path.pop_back();
        } 

### 5.查找最小的k个元素
###### 题目：输入n个整数，输出其中最小的k个。
        例如输入1，2，3，4，5，6，7和8这8个数字，则最小的4个数字为1，2，3和4。
>分析：这道题最简单的思路莫过于把输入的n个整数排序，这样排在最前面的k个数就是最小的k个数。只是这种思路的时间复杂度为O(nlogn)。我们试着寻找更快的解决思路。
>>我们可以开辟一个长度为k的数组。每次从输入的n个整数中读入一个数。如果数组中已经插入的元素少于k个，则将读入的整数直接放到数组中。否则长度为k的数组已经满了，不能再往数组里插入元素，只能替换了。如果读入的这个整数比数组中已有k个整数的最大值要小，则用读入的这个整数替换这个最大值；如果读入的整数比数组中已有k个整数的最大值还要大，则读入的这个整数不可能是最小的k个整数之一，抛弃这个整数。这种思路相当于只要排序k个整数，因此时间复杂可以降到O(n+nlogk)。通常情况下k要远小于n，所以这种办法要优于前面的思路。
>>>这是我能够想出来的最快的解决方案。不过从给面试官留下更好印象的角度出发，我们可以进一步把代码写得更漂亮一些。从上面的分析，当长度为k的数组已经满了之后，如果需要替换，每次替换的都是数组中的最大值。在常用的数据结构中，能够在O(1)时间里得到最大值的数据结构为最大堆。因此我们可以用堆（heap）来代替数组。
>>>>另外，自己重头开始写一个最大堆需要一定量的代码。我们现在不需要重新去发明车轮，因为前人早就发明出来了。同样，STL中的set和multiset为我们做了很好的堆的实现，我们可以拿过来用。既偷了懒，又给面试官留下熟悉STL的好印象，何乐而不为之？

- 参考代码：

        #include <set>
        #include <vector>
        #include <iostream>
        
        using namespace std;

        typedef multiset<int, greater<int> >  IntHeap;
        
        ///////////////////////////////////////////////////////////////////////
        // find k least numbers in a vector
        ///////////////////////////////////////////////////////////////////////
        void FindKLeastNumbers
        (
        	const vector<int>& data,               // a vector of data
        	IntHeap& leastNumbers,                 // k least numbers, output
        	unsigned int k                              
        )
        {
        	leastNumbers.clear();

        	if(k == 0 || data.size() < k)
        		return;
        
        	vector<int>::const_iterator iter = data.begin();
        	for(; iter != data.end(); ++ iter)
        	{
        		// if less than k numbers was inserted into leastNumbers
        		if((leastNumbers.size()) < k)
        			leastNumbers.insert(*iter);
        
        		// leastNumbers contains k numbers and it's full now
        		else
        		{
        			// first number in leastNumbers is the greatest one
        			IntHeap::iterator iterFirst = leastNumbers.begin();
        
        			// if is less than the previous greatest number 
        			if(*iter < *(leastNumbers.begin()))
        			{
        				// replace the previous greatest number
        				leastNumbers.erase(iterFirst);
        				leastNumbers.insert(*iter);
        			}
        		}
        	}
        }

### 6.判断整数序列是不是二元查找树的后序遍历结果 
###### 题目：输入一个整数数组，判断该数组是不是某二元查找树的后序遍历的结果。如果是返回true，否则返回false。 
        例如输入5、7、6、9、11、10、8，由于这一整数序列是如下树的后序遍历结果：
                 8
              /  \
              6    10
            / \    / \
          5   7   9  11
        因此返回true。
        如果输入7、4、6、5，没有哪棵树的后序遍历的结果是这个序列，因此返回false。
>分析：这是一道trilogy的笔试题，主要考查对二元查找树的理解。
在后续遍历得到的序列中，最后一个元素为树的根结点。从头开始扫描这个序列，比根结点小的元素都应该位于序列的左半部分；从第一个大于跟结点开始到跟结点前面的一个元素为止，所有元素都应该大于跟结点，因为这部分元素对应的是树的右子树。根据这样的划分，把序列划分为左右两部分，我们递归地确认序列的左、右两部分是不是都是二元查找树。

- 参考代码：

        using namespace std;
        
        ///////////////////////////////////////////////////////////////////////
        // Verify whether a squence of integers are the post order traversal
        // of a binary search tree (BST)
        // Input: squence - the squence of integers
        //        length  - the length of squence
        // Return: return ture if the squence is traversal result of a BST,
        //         otherwise, return false
        ///////////////////////////////////////////////////////////////////////
        bool verifySquenceOfBST(int squence[], int length)
        {
        	if(squence == NULL || length <= 0)
        		return false;
        
        	// root of a BST is at the end of post order traversal squence
        	int root = squence[length - 1];
        
        	// the nodes in left sub-tree are less than the root
        	int i = 0;
        	for(; i < length - 1; ++ i)
        	{
        		if(squence[i] > root)
        			break;
        	}
        
        	// the nodes in the right sub-tree are greater than the root
        	int j = i;
        	for(; j < length - 1; ++ j)
        	{
        		if(squence[j] < root)
        			return false;
        	}
        
        	// verify whether the left sub-tree is a BST
        	bool left = true;
        	if(i > 0)
        		left = verifySquenceOfBST(squence, i);
        
        	// verify whether the right sub-tree is a BST
        	bool right = true;
        	if(i < length - 1)
        		right = verifySquenceOfBST(squence + i, length - i - 1);
        
        	return (left && right);
        }

### 7.翻转句子中单词的顺序   
###### 题目：输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。句子中单词以空格符隔开。为简单起见，标点符号和普通字母一样处理。
        例如输入“I am a student.”，则输出“student. a am I”。
>分析：由于编写字符串相关代码能够反映程序员的编程能力和编程习惯，与字符串相关的问题一直是程序员笔试、面试题的热门题目。本题也曾多次受到包括微软在内的大量公司的青睐。
>>由于本题需要翻转句子，我们先颠倒句子中的所有字符。这时，不但翻转了句子中单词的顺序，而且单词内字符也被翻转了。我们再颠倒每个单词内的字符。由于单词内的字符被翻转两次，因此顺序仍然和输入时的顺序保持一致。
>>>还是以上面的输入为例子。翻转“I am a student.”中所有字符得到“.tneduts a ma I”，再翻转每个单词中字符的顺序得到“students. a am I”，正是符合要求的输出。

- 参考代码：

        ///////////////////////////////////////////////////////////////////////
        // Reverse a string between two pointers
        // Input: pBegin - the begin pointer in a string
        //        pEnd   - the end pointer in a string
        ///////////////////////////////////////////////////////////////////////
        void Reverse(char *pBegin, char *pEnd)
        {
        	if(pBegin == NULL || pEnd == NULL)
        		return;
        
        	while(pBegin < pEnd)
        	{
        		char temp = *pBegin;
        		*pBegin = *pEnd;
        		*pEnd = temp;
        
        		pBegin ++, pEnd --;
        	}
        }
        
        ///////////////////////////////////////////////////////////////////////
        // Reverse the word order in a sentence, but maintain the character
        // order inside a word
        // Input: pData - the sentence to be reversed
        ///////////////////////////////////////////////////////////////////////
        char* ReverseSentence(char *pData)
        {
        	if(pData == NULL)
        		return NULL;
        
        	char *pBegin = pData;
        	char *pEnd = pData;
        
        	while(*pEnd != '\0')
        		pEnd ++;
        	pEnd--;
        
        	// Reverse the whole sentence
        	Reverse(pBegin, pEnd);
        
        	// Reverse every word in the sentence
        	pBegin = pEnd = pData;
        	while(*pBegin != '\0')
        	{
        		if(*pBegin == ' ')
        		{
        			pBegin ++;
        			pEnd ++;
        			continue;
        		}
        		// A word is between with pBegin and pEnd, reverse it
        		else if(*pEnd == ' ' || *pEnd == '\0')
        		{
        			Reverse(pBegin, --pEnd);
        			pBegin = ++pEnd;
        		}
        		else
        		{
        			pEnd ++;
        		}
        	}
        
        	return pData;
        }

### 8.求1+2+...+n
###### 题目：求1+2+…+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字以及条件判断语句（A?B:C）。
>分析：这道题没有多少实际意义，因为在软件开发中不会有这么变态的限制。但这道题却能有效地考查发散思维能力，而发散思维能力能反映出对编程相关技术理解的深刻程度。
>>通常求1+2+…+n除了用公式n(n+1)/2之外，无外乎循环和递归两种思路。由于已经明确限制for和while的使用，循环已经不能再用了。同样，递归函数也需要用if语句或者条件判断语句来判断是继续递归下去还是终止递归，但现在题目已经不允许使用这两种语句了。
>>>我们仍然围绕循环做文章。循环只是让相同的代码执行n遍而已，我们完全可以不用for和while达到这个效果。比如定义一个类，我们new一含有n个这种类型元素的数组，那么该类的构造函数将确定会被调用n次。我们可以将需要执行的代码放到构造函数里。

- 如下代码正是基于这个思路：

        class Temp
        {
        public:
        	Temp() { ++ N; Sum += N; }
        
        	static void Reset() { N = 0; Sum = 0; }
        	static int GetSum() { return Sum; }
        
        private:
        	static int N;
        	static int Sum;
        };
        
        int Temp::N = 0;
        int Temp::Sum = 0;
        
        int solution1_Sum(int n)
        {
              Temp::Reset();
        
        	Temp *a = new Temp[n];
        	delete []a;
        	a = 0;
        
              return Temp::GetSum();
        }

>我们同样也可以围绕递归做文章。既然不能判断是不是应该终止递归，我们不妨定义两个函数。一个函数充当递归函数的角色，另一个函数处理终止递归的情况，我们需要做的就是在两个函数里二选一。从二选一我们很自然的想到布尔变量，比如ture（1）的时候调用第一个函数，false（0）的时候调用第二个函数。那现在的问题是如和把数值变量n转换成布尔值。如果对n连续做两次反运算，即!!n，那么非零的n转换为true，0转换为false。

- 有了上述分析，我们再来看下面的代码：

        class A;
        A* Array[2];
        
        class A
        {
        public:
        	virtual int Sum (int n) { return 0; }
        };
        
        class B: public A
        {
        public:
        	virtual int Sum (int n) { return Array[!!n]->Sum(n-1)+n; }
        };
        
        int solution2_Sum(int n)
        {
        	A a;
        	B b;
        	Array[0] = &a;
        	Array[1] = &b;
        
        	int value = Array[1]->Sum(n);
        
        	return value;
        }

>这种方法是用虚函数来实现函数的选择。当n不为零时，执行函数B::Sum；当n为0时，执行A::Sum。

- 我们也可以直接用函数指针数组，这样可能还更直接一些：

        typedef int (*fun)(int);
        
        int solution3_f1(int i) 
        {
        	return 0;
        }
        
        int solution3_f2(int i)
        {
        	fun f[2]={solution3_f1, solution3_f2}; 
        	return i+f[!!i](i-1);
        }

- 另外我们还可以让编译器帮我们来完成类似于递归的运算，比如如下代码：

        template <int n> struct solution4_Sum
        {
        	enum Value { N = solution4_Sum<n - 1>::N + n};
        };
        template <> struct solution4_Sum<1>
        {
        	enum Value { N = 1};
        };

>solution4_Sum<100>::N就是1+2+...+100的结果。当编译器看到solution4_Sum<100>时，就是为模板类solution4_Sum以参数100生成该类型的代码。但以100为参数的类型需要得到以99为参数的类型，因为solution4_Sum<100>::N=solution4_Sum<99>::N+100。这个过程会递归一直到参数为1的类型，由于该类型已经显式定义，编译器无需生成，递归编译到此结束。由于这个过程是在编译过程中完成的，因此要求输入n必须是在编译期间就能确定，不能动态输入。这是该方法最大的缺点。而且编译器对递归编译代码的递归深度是有限制的，也就是要求n不能太大。


