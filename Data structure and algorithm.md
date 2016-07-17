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

### 9.查找链表中倒数第k个结点
###### 题目：输入一个单向链表，输出该链表中倒数第k个结点。链表的倒数第0个结点为链表的尾指针。链表结点定义如下： 
        struct ListNode
        {
        	int       m_nKey;
        	ListNode* m_pNext;
        };
>分析：为了得到倒数第k个结点，很自然的想法是先走到链表的尾端，再从尾端回溯k步。可是输入的是单向链表，只有从前往后的指针而没有从后往前的指针。因此我们需要打开我们的思路。
既然不能从尾结点开始遍历这个链表，我们还是把思路回到头结点上来。假设整个链表有n个结点，那么倒数第k个结点是从头结点开始的第n-k-1个结点（从0开始计数）。如果我们能够得到链表中结点的个数n，那我们只要从头结点开始往后走n-k-1步就可以了。如何得到结点数n？这个不难，只需要从头开始遍历链表，每经过一个结点，计数器加一就行了。
>>这种思路的时间复杂度是O(n)，但需要遍历链表两次。第一次得到链表中结点个数n，第二次得到从头结点开始的第n¬-k-1个结点即倒数第k个结点。
>>>如果链表的结点数不多，这是一种很好的方法。但如果输入的链表的结点个数很多，有可能不能一次性把整个链表都从硬盘读入物理内存，那么遍历两遍意味着一个结点需要两次从硬盘读入到物理内存。我们知道把数据从硬盘读入到内存是非常耗时间的操作。我们能不能把链表遍历的次数减少到1？如果可以，将能有效地提高代码执行的时间效率。
>>>>如果我们在遍历时维持两个指针，第一个指针从链表的头指针开始遍历，在第k-1步之前，第二个指针保持不动；在第k-1步开始，第二个指针也开始从链表的头指针开始遍历。由于两个指针的距离保持在k-1，当第一个（走在前面的）指针到达链表的尾结点时，第二个指针（走在后面的）指针正好是倒数第k个结点。
>>>>>这种思路只需要遍历链表一次。对于很长的链表，只需要把每个结点从硬盘导入到内存一次。因此这一方法的时间效率前面的方法要高。

- 思路一的参考代码：

        ///////////////////////////////////////////////////////////////////////
        // Find the kth node from the tail of a list
        // Input: pListHead - the head of list
        //        k         - the distance to the tail
        // Output: the kth node from the tail of a list
        ///////////////////////////////////////////////////////////////////////
        ListNode* FindKthToTail_Solution1(ListNode* pListHead, unsigned int k)
        {
        	if(pListHead == NULL)
        		return NULL;
        
        	// count the nodes number in the list
        	ListNode *pCur = pListHead;
        	unsigned int nNum = 0;
        	while(pCur->m_pNext != NULL)
        	{
        		pCur = pCur->m_pNext;
        		nNum ++;
        	}
        
        	// if the number of nodes in the list is less than k
        	// do nothing
        	if(nNum < k)
        		return NULL;
        
        	// the kth node from the tail of a list 
        	// is the (n - k)th node from the head
        	pCur = pListHead;
        	for(unsigned int i = 0; i < nNum - k; ++ i)
        		pCur = pCur->m_pNext;
        
        	return pCur;
        }

- 思路二的参考代码：

        ///////////////////////////////////////////////////////////////////////
        // Find the kth node from the tail of a list
        // Input: pListHead - the head of list
        //        k         - the distance to the tail
        // Output: the kth node from the tail of a list
        ///////////////////////////////////////////////////////////////////////
        ListNode* FindKthToTail_Solution2(ListNode* pListHead, unsigned int k)
        {
        	if(pListHead == NULL)
        		return NULL;
        
        	ListNode *pAhead = pListHead;
        	ListNode *pBehind = NULL;
        
        	for(unsigned int i = 0; i < k; ++ i)
        	{
        		if(pAhead->m_pNext != NULL)
        			pAhead = pAhead->m_pNext;
        		else
        		{
        			// if the number of nodes in the list is less than k, 
        			// do nothing
        			return NULL;
        		}
        	}
        
        	pBehind = pListHead;
        
        	// the distance between pAhead and pBehind is k
        	// when pAhead arrives at the tail, p
        	// Behind is at the kth node from the tail
        	while(pAhead->m_pNext != NULL)
        	{
        		pAhead = pAhead->m_pNext;
        		pBehind = pBehind->m_pNext;
        	}

        	return pBehind;
        }

>讨论：这道题的代码有大量的指针操作。在软件开发中，错误的指针操作是大部分问题的根源。因此每个公司都希望程序员在操作指针时有良好的习惯，比如使用指针之前判断是不是空指针。这些都是编程的细节，但如果这些细节把握得不好，很有可能就会和心仪的公司失之交臂。
>>另外，这两种思路对应的代码都含有循环。含有循环的代码经常出的问题是在循环结束条件的判断。是该用小于还是小于等于？是该用k还是该用k-1？由于题目要求的是从0开始计数，而我们的习惯思维是从1开始计数，因此首先要想好这些边界条件再开始编写代码，再者要在编写完代码之后再用边界值、边界值减1、边界值加1都运行一次（在纸上写代码就只能在心里运行了）。
>>>扩展：和这道题类似的题目还有：输入一个单向链表。如果该链表的结点数为奇数，输出中间的结点；如果链表结点数为偶数，输出中间两个结点前面的一个。如果各位感兴趣，请自己分析并编写代码。

### 在排序数组中查找和为给定值的两个数字
######题目：输入一个已经按升序排序过的数组和一个数字，在数组中查找两个数，使得它们的和正好是输入的那个数字。要求时间复杂度是O(n)。如果有多对数字的和等于输入的数字，输出任意一对即可。
        例如输入数组1、2、4、7、11、15和数字15。由于4+11=15，因此输出4和11。
>分析：如果我们不考虑时间复杂度，最简单想法的莫过去先在数组中固定一个数字，再依次判断数组中剩下的n-1个数字与它的和是不是等于输入的数字。可惜这种思路需要的时间复杂度是O(n2)。
>>我们假设现在随便在数组中找到两个数。如果它们的和等于输入的数字，那太好了，我们找到了要找的两个数字；如果小于输入的数字呢？我们希望两个数字的和再大一点。由于数组已经排好序了，我们是不是可以把较小的数字的往后面移动一个数字？因为排在后面的数字要大一些，那么两个数字的和也要大一些，就有可能等于输入的数字了；同样，当两个数字的和大于输入的数字的时候，我们把较大的数字往前移动，因为排在数组前面的数字要小一些，它们的和就有可能等于输入的数字了。
>>>我们把前面的思路整理一下：最初我们找到数组的第一个数字和最后一个数字。当两个数字的和大于输入的数字时，把较大的数字往前移动；当两个数字的和小于数字时，把较小的数字往后移动；当相等时，打完收工。这样扫描的顺序是从数组的两端向数组的中间扫描。
>>>>问题是这样的思路是不是正确的呢？这需要严格的数学证明。感兴趣的读者可以自行证明一下。

- 参考代码：

        ///////////////////////////////////////////////////////////////////////
        // Find two numbers with a sum in a sorted array
        // Output: ture is found such two numbers, otherwise false
        ///////////////////////////////////////////////////////////////////////
        bool FindTwoNumbersWithSum
        (
        	int data[],           // a sorted array
        	unsigned int length,  // the length of the sorted array   	
        	int sum,              // the sum
        	int& num1,            // the first number, output
        	int& num2             // the second number, output
        )
        {
        	bool found = false;
        	if(length < 1)
        		return found;
        
        	int ahead = length - 1;
        	int behind = 0;
        
        	while(ahead > behind)
        	{
        		long long curSum = data[ahead] + data[behind];
        
        		// if the sum of two numbers is equal to the input
        		// we have found them
        		if(curSum == sum)
        		{
        			num1 = data[behind];
        			num2 = data[ahead];
        			found = true;
        			break;
        		}
        		// if the sum of two numbers is greater than the input
        		// decrease the greater number
        		else if(curSum > sum)
        			ahead --;
        		// if the sum of two numbers is less than the input
        		// increase the less number
        		else
        			behind ++;
        	}
        
        	return found;
        }

>扩展：如果输入的数组是没有排序的，但知道里面数字的范围，其他条件不变，如和在O(n)时间里找到这两个数字？

### 11.求二元查找树的镜像
###### 题目：输入一颗二元查找树，将该树转换为它的镜像，即在转换后的二元查找树中，左子树的结点都大于右子树的结点。用递归和循环两种方法完成树的镜像转换。 
        例如输入：
             8
            /  \
          6      10
         /\       /\
        5  7    9   11
        输出：
              8
            /  \
          10    6
         /\      /\
        11  9  7  5
        定义二元查找树的结点为：
        struct BSTreeNode // a node in the binary search tree (BST)
        {
        	int          m_nValue; // value of node
        	BSTreeNode  *m_pLeft;  // left child of node
        	BSTreeNode  *m_pRight; // right child of node
        };
>分析：尽管我们可能一下子不能理解镜像是什么意思，但上面的例子给我们的直观感觉，就是交换结点的左右子树。我们试着在遍历例子中的二元查找树的同时来交换每个结点的左右子树。遍历时首先访问头结点8，我们交换它的左右子树得到：

              8
            /  \
          10    6
         /\      /\
        9  11  5  7
>我们发现两个结点6和10的左右子树仍然是左结点的值小于右结点的值，我们再试着交换他们的左右子树，得到：

              8
            /  \
          10    6
         /\      /\
        11  9  7   5
        刚好就是要求的输出。
>上面的分析印证了我们的直觉：在遍历二元查找树时每访问到一个结点，交换它的左右子树。这种思路用递归不难实现，将遍历二元查找树的代码稍作修改就可以了。

- 参考代码如下：

        ///////////////////////////////////////////////////////////////////////
        // Mirror a BST (swap the left right child of each node) recursively
        // the head of BST in initial call
        ///////////////////////////////////////////////////////////////////////
        void MirrorRecursively(BSTreeNode *pNode)
        {
        	if(!pNode)
        		return;
        
        	// swap the right and left child sub-tree
        	BSTreeNode *pTemp = pNode->m_pLeft;
        	pNode->m_pLeft = pNode->m_pRight;
        	pNode->m_pRight = pTemp;
        	
        // mirror left child sub-tree if not null
        	if(pNode->m_pLeft)
        		MirrorRecursively(pNode->m_pLeft);  
        
        	// mirror right child sub-tree if not null
        	if(pNode->m_pRight)
        		MirrorRecursively(pNode->m_pRight); 
        }

>由于递归的本质是编译器生成了一个函数调用的栈，因此用循环来完成同样任务时最简单的办法就是用一个辅助栈来模拟递归。首先我们把树的头结点放入栈中。在循环中，只要栈不为空，弹出栈的栈顶结点，交换它的左右子树。如果它有左子树，把它的左子树压入栈中；如果它有右子树，把它的右子树压入栈中。这样在下次循环中就能交换它儿子结点的左右子树了。

- 参考代码如下：

        ///////////////////////////////////////////////////////////////////////
        // Mirror a BST (swap the left right child of each node) Iteratively
        // Input: pTreeHead: the head of BST
        ///////////////////////////////////////////////////////////////////////
        void MirrorIteratively(BSTreeNode *pTreeHead)
        {
        	if(!pTreeHead)
        		return;
        
        	std::stack<BSTreeNode*>stackTreeNode;
        	stackTreeNode.push(pTreeHead);
        
        while(stackTreeNode.size())
        	{
        		BSTreeNode *pNode = stackTreeNode.top();
        		stackTreeNode.pop();
        
        		// swap the right and left child sub-tree
        		BSTreeNode *pTemp = pNode->m_pLeft;
        		pNode->m_pLeft = pNode->m_pRight;
        		pNode->m_pRight = pTemp;
        
        		// push left child sub-tree into stack if not null
        		if(pNode->m_pLeft)
        			stackTreeNode.push(pNode->m_pLeft);
        
        		// push right child sub-tree into stack if not null
        		if(pNode->m_pRight)
			stackTreeNode.push(pNode->m_pRight);
        	}
        }

### 12.－从上往下遍历二元树
###### 题目：输入一颗二元树，从上往下按层打印树的每个结点，同一层中按照从左往右的顺序打印。 
        例如输入
             8
            /  \
           6    10
         /\     /\
        5  7   9  11
        输出8   6   10   5   7   9   11。
>分析：这曾是微软的一道面试题。这道题实质上是要求遍历一棵二元树，只不过不是我们熟悉的前序、中序或者后序遍历。
>>我们从树的根结点开始分析。自然先应该打印根结点8，同时为了下次能够打印8的两个子结点，我们应该在遍历到8时把子结点6和10保存到一个数据容器中。现在数据容器中就有两个元素6 和10了。按照从左往右的要求，我们先取出6访问。打印6的同时要把6的两个子结点5和7放入数据容器中，此时数据容器中有三个元素10、5和7。接下来我们应该从数据容器中取出结点10访问了。注意10比5和7先放入容器，此时又比5和7先取出，就是我们通常说的先入先出。因此不难看出这个数据容器的类型应该是个队列。
>>>既然已经确定数据容器是一个队列，现在的问题变成怎么实现队列了。实际上我们无需自己动手实现一个，因为STL已经为我们实现了一个很好的deque（两端都可以进出的队列），我们只需要拿过来用就可以了。
>>>>我们知道树是图的一种特殊退化形式。同时如果对图的深度优先遍历和广度优先遍历有比较深刻的理解，将不难看出这种遍历方式实际上是一种广度优先遍历。因此这道题的本质是在二元树上实现广度优先遍历。

- 参考代码：

        #include <deque>
        #include <iostream>
        using namespace std;
        
        struct BTreeNode // a node in the binary tree
        {
        	int         m_nValue; // value of node
        	BTreeNode  *m_pLeft;  // left child of node
        	BTreeNode  *m_pRight; // right child of node
        };
        
        ///////////////////////////////////////////////////////////////////////
        // Print a binary tree from top level to bottom level
        // Input: pTreeRoot - the root of binary tree
        ///////////////////////////////////////////////////////////////////////
        void PrintFromTopToBottom(BTreeNode *pTreeRoot)
        {
        	if(!pTreeRoot)
        		return;
        
        	// get a empty queue
        	deque<BTreeNode *> dequeTreeNode;
        
        	// insert the root at the tail of queue
        	dequeTreeNode.push_back(pTreeRoot);
        
        	while(dequeTreeNode.size())
        	{
        		// get a node from the head of queue
        		BTreeNode *pNode = dequeTreeNode.front();
        		dequeTreeNode.pop_front();
        
        		// print the node
        		cout << pNode->m_nValue << ' ';
        
        		// print its left child sub-tree if it has
        		if(pNode->m_pLeft)
        			dequeTreeNode.push_back(pNode->m_pLeft);
        		// print its right child sub-tree if it has
        		if(pNode->m_pRight)
        			dequeTreeNode.push_back(pNode->m_pRight);
        	}
        }

### 13.第一个只出现一次的字符
###### 题目：在一个字符串中找到第一个只出现一次的字符。如输入abaccdeff，则输出b。 
>分析：这道题是2006年google的一道笔试题。
看到这道题时，最直观的想法是从头开始扫描这个字符串中的每个字符。当访问到某字符时拿这个字符和后面的每个字符相比较，如果在后面没有发现重复的字符，则该字符就是只出现一次的字符。如果字符串有n个字符，每个字符可能与后面的O(n)个字符相比较，因此这种思路时间复杂度是O(n2)。我们试着去找一个更快的方法。
>>由于题目与字符出现的次数相关，我们是不是可以统计每个字符在该字符串中出现的次数？要达到这个目的，我们需要一个数据容器来存放每个字符的出现次数。在这个数据容器中可以根据字符来查找它出现的次数，也就是说这个容器的作用是把一个字符映射成一个数字。在常用的数据容器中，哈希表正是这个用途。
>>>哈希表是一种比较复杂的数据结构。由于比较复杂，STL中没有实现哈希表，因此需要我们自己实现一个。但由于本题的特殊性，我们只需要一个非常简单的哈希表就能满足要求。由于字符（char）是一个长度为8的数据类型，因此总共有可能256种可能。于是我们创建一个长度为256的数组，每个字母根据其ASCII码值作为数组的下标对应数组的对应项，而数组中存储的是每个字符对应的次数。这样我们就创建了一个大小为256，以字符ASCII码为键值的哈希表。
>>>>我们第一遍扫描这个数组时，每碰到一个字符，在哈希表中找到对应的项并把出现的次数增加一次。这样在进行第二次扫描时，就能直接从哈希表中得到每个字符出现的次数了。

- 参考代码如下：

        ///////////////////////////////////////////////////////////////////////
        // Find the first char which appears only once in a string
        // Input: pString - the string
        // Output: the first not repeating char if the string has, otherwise 0
        ///////////////////////////////////////////////////////////////////////
        char FirstNotRepeatingChar(char* pString)
        {
        	// invalid input
        	if(!pString)
        		return 0;
        
        	// get a hash table, and initialize it 
        		constinttableSize =256;
        unsignedinthashTable[tableSize];
        for(unsignedinti = 0; i<tableSize; ++ i)
        hashTable[i] = 0;
        
        	// get the how many times each char appears in the string
        	char* pHashKey = pString;
        	while(*(pHashKey) != '\0')
        		hashTable[*(pHashKey++)] ++;
        
        	// find the first char which appears only once in a string
        	pHashKey = pString;
        	while(*pHashKey != '\0')
        	{
        		if(hashTable[*pHashKey] == 1)
        			return *pHashKey;
        
        		pHashKey++;
        	}
        
        	// if the string is empty 
        	// or every char in the string appears at least twice
        	return 0;
        } 

### 14.圆圈中最后剩下的数字 
###### 题目：n个数字（0,1,…,n-1）形成一个圆圈，从数字0开始，每次从这个圆圈中删除第m个数字（第一个为当前数字本身，第二个为当前数字的下一个数字）。当一个数字删除后，从被删除数字的下一个继续删除第m个数字。求出在这个圆圈中剩下的最后一个数字。
>分析：既然题目有一个数字圆圈，很自然的想法是我们用一个数据结构来模拟这个圆圈。在常用的数据结构中，我们很容易想到用环形列表。我们可以创建一个总共有m个数字的环形列表，然后每次从这个列表中删除第m个元素。
>>在参考代码中，我们用STL中std::list来模拟这个环形列表。由于list并不是一个环形的结构，因此每次跌代器扫描到列表末尾的时候，要记得把跌代器移到列表的头部。这样就是按照一个圆圈的顺序来遍历这个列表了。
>>>这种思路需要一个有n个结点的环形列表来模拟这个删除的过程，因此内存开销为O(n)。而且这种方法每删除一个数字需要m步运算，总共有n个数字，因此总的时间复杂度是O(mn)。当m和n都很大的时候，这种方法是很慢的。
>>>>接下来我们试着从数学上分析出一些规律。首先定义最初的n个数字（0,1,…,n-1）中最后剩下的数字是关于n和m的方程为f(n,m)。
>>>>>在这n个数字中，第一个被删除的数字是m%n-1，为简单起见记为k。那么删除k之后的剩下n-1的数字为0,1,…,k-1,k+1,…,n-1，并且下一个开始计数的数字是k+1。相当于在剩下的序列中，k+1排到最前面，从而形成序列k+1,…,n-1,0,…k-1。该序列最后剩下的数字也应该是关于n和m的函数。由于这个序列的规律和前面最初的序列不一样（最初的序列是从0开始的连续序列），因此该函数不同于前面函数，记为f’(n-1,m)。最初序列最后剩下的数字f(n,m)一定是剩下序列的最后剩下数字f’(n-1,m)，所以f(n,m)=f’(n-1,m)。
>>>>>>接下来我们把剩下的的这n-1个数字的序列k+1,…,n-1,0,…k-1作一个映射，

        映射的结果是形成一个从0到n-2的序列：
        k+1    ->    0
        k+2    ->    1
        …
        n-1    ->    n-k-2
        0   ->    n-k-1
        …
        k-1   ->   n-2
>把映射定义为p，则p(x)=(x-k-1)%n，即如果映射前的数字是x，则映射后的数字是(x-k-1)%n。对应的逆映射是p-1(x)=(x+k+1)%n。
>>由于映射之后的序列和最初的序列有同样的形式，都是从0开始的连续序列，因此仍然可以用函数f来表示，记为f(n-1,m)。根据我们的映射规则，映射之前的序列最后剩下的数字f’(n-1,m)= p-1 [f(n-1,m)]=[f(n-1,m)+k+1]%n。把k=m%n-1代入得到f(n,m)=f’(n-1,m)=[f(n-1,m)+m]%n。
经过上面复杂的分析，我们终于找到一个递归的公式。要得到n个数字的序列的最后剩下的数字，只需要得到n-1个数字的序列的最后剩下的数字，并可以依此类推。当n=1时，也就是序列中开始只有一个数字0，那么很显然最后剩下的数字就是0。

        我们把这种关系表示为：
                 0                  n=1
        f(n,m)={
                [f(n-1,m)+m]%n     n>1
>尽管得到这个公式的分析过程非常复杂，但它用递归或者循环都很容易实现。最重要的是，这是一种时间复杂度为O(n)，空间复杂度为O(1)的方法，因此无论在时间上还是空间上都优于前面的思路。

- 思路一的参考代码：

        ///////////////////////////////////////////////////////////////////////
        // n integers (0, 1, ... n - 1) form a circle. Remove the mth from 
        // the circle at every time. Find the last number remaining 
        // Input: n - the number of integers in the circle initially
        //        m - remove the mth number at every time
        // Output: the last number remaining when the input is valid,
        //         otherwise -1
        ///////////////////////////////////////////////////////////////////////
        int LastRemaining_Solution1(unsigned int n, unsigned int m)
        {
        	// invalid input
        	if(n < 1 || m < 1)
        		return -1;
        
        	unsigned int i = 0;
        
        	// initiate a list with n integers (0, 1, ... n - 1)
        	list<int> integers;
        	for(i = 0; i < n; ++ i)
        		integers.push_back(i);
        
        	list<int>::iterator curinteger = integers.begin();
            	while(integers.size() > 1)
        	{
        		// find the mth integer. Note that std::list is not a circle
        		// so we should handle it manually
        		for(int i = 1; i < m; ++ i)
        		{
        			curinteger ++;
        			if(curinteger == integers.end())
        				curinteger = integers.begin();
        		}
        
        		// remove the mth integer. Note that std::list is not a circle
        		// so we should handle it manually
        		list<int>::iterator nextinteger = ++ curinteger;
        		if(nextinteger == integers.end())
        			nextinteger = integers.begin();
        
        		-- curinteger;
        		integers.erase(curinteger);
        		curinteger = nextinteger;
        	}
        
        	return *(curinteger);
        }

- 思路二的参考代码：

        ///////////////////////////////////////////////////////////////////////
        // n integers (0, 1, ... n - 1) form a circle. Remove the mth from 
        // the circle at every time. Find the last number remaining 
        // Input: n - the number of integers in the circle initially
        //        m - remove the mth number at every time
        // Output: the last number remaining when the input is valid,
        //         otherwise -1
        ///////////////////////////////////////////////////////////////////////
        int LastRemaining_Solution2(int n, unsigned int m)
        {
        	// invalid input
        	if(n <= 0 || m < 0)
        		return -1;
        
        	// if there are only one integer in the circle initially,
        	// of course the last remaining one is 0
        	int lastinteger = 0;
        
        	// find the last remaining one in the circle with n integers
        	for (int i = 2; i <= n; i ++) 
        		lastinteger = (lastinteger + m) % i;
        
        	return lastinteger;
        }
>如果对两种思路的时间复杂度感兴趣的读者可以把n和m的值设的稍微大一点，比如十万这个数量级的数字，运行的时候就能明显感觉出这两种思路写出来的代码时间效率大不一样。

### 15.含有指针成员的类的拷贝
######题目：下面是一个数组类的声明与实现。请分析这个类有什么问题，并针对存在的问题提出几种解决方案。

        template<typename T> class Array
        {
        public:
        	Array(unsigned arraySize):data(0), size(arraySize)
        	{
        		if(size > 0)
        			data = new T[size];
        	}
        
        	~Array()
        	{
        		if(data) delete[] data;
        	}
        
        	void setValue(unsigned index, const T& value)
        	{
        		if(index < size)
        			data[index] = value;
        	}
        
        	T getValue(unsigned index) const
        	{
        		if(index < size)
        			return data[index];
        		else
        			return T();
        	}
        
        private:
        	T* data;
        	unsigned size;
        };
>分析：我们注意在类的内部封装了用来存储数组数据的指针。软件存在的大部分问题通常都可以归结指针的不正确处理。

        这个类只提供了一个构造函数，而没有定义构造拷贝函数和重载拷贝运算符函数。当这     个类的用户按照下面的方式声明并实例化该类的一个实例
        Array A(10);
        Array B(A);
        或者按照下面的方式把该类的一个实例赋值给另外一个实例
        Array A(10);
        Array B(10);
        B=A;
>编译器将调用其自动生成的构造拷贝函数或者拷贝运算符的重载函数。在编译器生成的缺省的构造拷贝函数和拷贝运算符的重载函数，对指针实行的是按位拷贝，仅仅只是拷贝指针的地址，而不会拷贝指针的内容。因此在执行完前面的代码之后，A.data和B.data指向的同一地址。当A或者B中任意一个结束其生命周期调用析构函数时，会删除data。由于他们的data指向的是同一个地方，两个实例的data都被删除了。但另外一个实例并不知道它的data已经被删除了，当企图再次用它的data的时候，程序就会不可避免地崩溃。
>>由于问题出现的根源是调用了编译器生成的缺省构造拷贝函数和拷贝运算符的重载函数。一个最简单的办法就是禁止使用这两个函数。于是我们可以把这两个函数声明为私有函数，如果类的用户企图调用这两个函数，将不能通过编译。

- 实现的代码如下：

        private:
        	Array(const Array& copy);
        	const Array& operator = (const Array& copy); 
        最初的代码存在问题是因为不同实例的data指向的同一地址，删除一个实例的data会把     另外一个实例的data也同时删除。因此我们还可以让构造拷贝函数或者拷贝运算符的重载函     数拷贝的不只是地址，而是数据。由于我们重新存储了一份数据，这样一个实例删除的时候     ，对另外一个实例没有影响。这种思路我们称之为深度拷贝。

- 实现的代码如下：
        public:
        	Array(const Array& copy):data(0), size(copy.size)
        	{
        		if(size > 0)
        		{
        			data = new T[size];
        			for(int i = 0; i < size; ++ i)
        				setValue(i, copy.getValue(i));
        		}
        	}

        	const Array& operator = (const Array& copy)
        	{
        		if(this == &copy)
        			return *this;
        
        		if(data != NULL)
        		{
        			delete []data;
        			data = NULL;
        		}
        
        		size = copy.size;
        		if(size > 0)
        		{
        			data = new T[size];
        			for(int i = 0; i < size; ++ i)
        				setValue(i, copy.getValue(i));
        		}
        	}

>为了防止有多个指针指向的数据被多次删除，我们还可以保存究竟有多少个指针指向该数据。只有当没有任何指针指向该数据的时候才可以被删除。这种思路通常被称之为引用计数技术。在构造函数中，引用计数初始化为1；每当把这个实例赋值给其他实例或者以参数传给其他实例的构造拷贝函数的时候，引用计数加1，因为这意味着又多了一个实例指向它的data；每次需要调用析构函数或者需要把data赋值为其他数据的时候，引用计数要减1，因为这意味着指向它的data的指针少了一个。当引用计数减少到0的时候，data已经没有任何实例指向它了，这个时候就可以安全地删除。

- 实现的代码如下：

        public:
        	Array(unsigned arraySize)
        		:data(0), size(arraySize), count(new unsigned int)
        	{
        		*count = 1;
        		if(size > 0)
        			data = new T[size];
        	}
        
        	Array(const Array& copy)
        		: size(copy.size), data(copy.data), count(copy.count)
        	{
        		++ (*count);
        	}
        
        	~Array()
        	{
        		Release();
        	}
        
        	const Array& operator = (const Array& copy)
        	{
        		if(data == copy.data)
        			return *this;
        
        		Release();
        
        		data = copy.data;
        		size = copy.size;
        		count = copy.count;
        		++(*count);
        	}
        
        private:
        	void Release()
        	{
        		--(*count);
        		if(*count == 0)
        		{
        			if(data)
        			{
        				delete []data;
        				data = NULL;
        			}
        
        			delete count;
        			count = 0;
        		}
        	}
        
        	unsigned int *count; 

### 16.O(logn)求Fibonacci数列
###### 题目：定义Fibonacci数列如下： 
                /  0                      n=0
        f(n)=      1                      n=1
                \  f(n-1)+f(n-2)          n=2
        输入n，用最快的方法求该数列的第n项。
>分析：在很多C语言教科书中讲到递归函数的时候，都会用Fibonacci作为例子。因此很多程序员对这道题的递归解法非常熟悉，看到题目就能写出如下的递归求解的代码。

        ///////////////////////////////////////////////////////////////////////
        // Calculate the nth item of Fibonacci Series recursively
        ///////////////////////////////////////////////////////////////////////
        long long Fibonacci_Solution1(unsigned int n)
        {
        	int result[2] = {0, 1};
        	if(n < 2)
        		return result[n];
        
        	return Fibonacci_Solution1(n - 1) + Fibonacci_Solution1(n - 2);
        }
>但是，教科书上反复用这个题目来讲解递归函数，并不能说明递归解法最适合这道题目。我们以求解f(10)作为例子来分析递归求解的过程。要求得f(10)，需要求得f(9)和f(8)。同样，要求得f(9)，要先求得f(8)和f(7)……我们用树形结构来表示这种依赖关系

                          f(10)
                       /        \
                    f(9)         f(8)
                  /     \       /    \
               f(8)     f(7)  f(6)   f(5)
              /   \     /   \ 
           f(7)  f(6)  f(6) f(5)
>我们不难发现在这棵树中有很多结点会重复的，而且重复的结点数会随着n的增大而急剧增加。这意味这计算量会随着n的增大而急剧增大。事实上，用递归方法计算的时间复杂度是以n的指数的方式递增的。大家可以求Fibonacci的第100项试试，感受一下这样递归会慢到什么程度。在我的机器上，连续运行了一个多小时也没有出来结果。
>>其实改进的方法并不复杂。上述方法之所以慢是因为重复的计算太多，只要避免重复计算就行了。比如我们可以把已经得到的数列中间项保存起来，如果下次需要计算的时候我们先查找一下，如果前面已经计算过了就不用再次计算了。
>>更简单的办法是从下往上计算，首先根据f(0)和f(1)算出f(2)，在根据f(1)和f(2)算出f(3)……依此类推就可以算出第n项了。很容易理解，这种思路的时间复杂度是O(n)。

        ///////////////////////////////////////////////////////////////////////
        // Calculate the nth item of Fibonacci Series iteratively
        ///////////////////////////////////////////////////////////////////////
        long long Fibonacci_Solution2(unsigned n)
        {
        	int result[2] = {0, 1};
        	if(n < 2)
        		return result[n];
        
        	long long  fibNMinusOne = 1;
        	long long  fibNMinusTwo = 0;
        	long long  fibN = 0;
        	for(unsigned int i = 2; i <= n; ++ i)
        	{
        		fibN = fibNMinusOne + fibNMinusTwo;
        
        		fibNMinusTwo = fibNMinusOne;
        		fibNMinusOne = fibN;
        	}
        
        	return fibN;
        }
>这还不是最快的方法。下面介绍一种时间复杂度是O(logn)的方法。在介绍这种方法之前，先介绍一个数学公式：{f(n), f(n-1), f(n-1), f(n-2)} ={1, 1, 1,0}n-1
(注：{f(n+1), f(n), f(n),f(n-1)}表示一个矩阵。在矩阵中第一行第一列是f(n+1)，第一行第二列是f(n)，第二行第一列是f(n)，第二行第二列是f(n-1)。)
>>有了这个公式，要求得f(n)，我们只需要求得矩阵{1, 1, 1,0}的n-1次方，因为矩阵{1, 1, 1,0}的n-1次方的结果的第一行第一列就是f(n)。这个数学公式用数学归纳法不难证明。感兴趣的朋友不妨自己证明一下。
>>>现在的问题转换为求矩阵{1,1,1,0}的乘方。如果简单第从0开始循环，n次方将需要n次运算，并不比前面的方法要快。但我们可以考虑乘方的如下性质：

                /  an/2*an/2                      n为偶数时
        an=
               \  a(n-1)/2*a(n-1)/2            n为奇数时
>要求得n次方，我们先求得n/2次方，再把n/2的结果平方一下。如果把求n次方的问题看成一个大问题，把求n/2看成一个较小的问题。这种把大问题分解成一个或多个小问题的思路我们称之为分治法。这样求n次方就只需要logn次运算了。
>>实现这种方式时，首先需要定义一个2×2的矩阵，并且定义好矩阵的乘法以及乘方运算。当这些运算定义好了之后，剩下的事情就变得非常简单。

- 完整的实现代码如下所示。

        #include <cassert>
        
        ///////////////////////////////////////////////////////////////////////
        // A 2 by 2 matrix
        ///////////////////////////////////////////////////////////////////////
        struct Matrix2By2
        {
        	Matrix2By2
        	(
        		long long m00 = 0, 
        		long long m01 = 0, 
        		long long m10 = 0, 
        		long long m11 = 0
        	)
        	:m_00(m00), m_01(m01), m_10(m10), m_11(m11) 
        	{
        	}
        
        	long long m_00;
        	long long m_01;
        	long long m_10;
        	long long m_11;
        };
        
        ///////////////////////////////////////////////////////////////////////
        // Multiply two matrices
        // Input: matrix1 - the first matrix
        //        matrix2 - the second matrix
        //Output: the production of two matrices
        ///////////////////////////////////////////////////////////////////////
        Matrix2By2 MatrixMultiply
        (
        	const Matrix2By2& matrix1, 
        	const Matrix2By2& matrix2
        )
        {
        	return Matrix2By2(
        		matrix1.m_00 * matrix2.m_00 + matrix1.m_01 * matrix2.m_10,
        		matrix1.m_00 * matrix2.m_01 + matrix1.m_01 * matrix2.m_11,
        		matrix1.m_10 * matrix2.m_00 + matrix1.m_11 * matrix2.m_10,
        		matrix1.m_10 * matrix2.m_01 + matrix1.m_11 * matrix2.m_11);
        }
        
        ///////////////////////////////////////////////////////////////////////
        // The nth power of matrix 
        // 1  1
        // 1  0
        ///////////////////////////////////////////////////////////////////////
        Matrix2By2 MatrixPower(unsigned int n)
        {
        	assert(n > 0);
        
        	Matrix2By2 matrix;
        	if(n == 1)
        	{
        		matrix = Matrix2By2(1, 1, 1, 0);
        	}
        	else if(n % 2 == 0)
        	{
        		matrix = MatrixPower(n / 2);
        		matrix = MatrixMultiply(matrix, matrix);
        	}
        	else if(n % 2 == 1)
        	{
        		matrix = MatrixPower((n - 1) / 2);
        		matrix = MatrixMultiply(matrix, matrix);
        		matrix = MatrixMultiply(matrix, Matrix2By2(1, 1, 1, 0));
        	}
        
        	return matrix;
        }
        
        ///////////////////////////////////////////////////////////////////////
        // Calculate the nth item of Fibonacci Series using devide and conquer
        ///////////////////////////////////////////////////////////////////////
        long long Fibonacci_Solution3(unsigned int n)
        {
        	int result[2] = {0, 1};
        	if(n < 2)
        		return result[n];
        
        	Matrix2By2 PowerNMinus2 = MatrixPower(n - 1);
        	return PowerNMinus2.m_00;
        }

