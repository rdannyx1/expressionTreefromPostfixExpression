# expressionTreefromPostfixExpression

#include<iostream>
#include<string>
#include<fstream>
using namespace std;
string PostfixToPrefix(char * expression, int size);
int OperatorAndItsPrecedence(char temporary);
char *reGrow(char *array, int size);
void displayMenu();
void mainMenu(int dot);
template <class T>
class Stack
{
    T *array;
    int capacity, size;

 

public:
    Stack()
    {
        this->array = nullptr;
        this->capacity = 30;
        this->array = new T[capacity];
        this->size = 0;
    }
    Stack(int capacity)
    {
        this->array = nullptr;
        this->capacity = capacity;
        this->array = new T[capacity];
        this->size = 0;
    }
    bool isFull()
    {
        if (size == capacity)
            return true;
        return false;
    }
    void push(T value)
    {
        if (!isFull())
        {
            array[size++] = value;
        }
        else
        {
            array = reGrow(array, size, capacity);
            array[size++] = value;
        }
    }
    bool isEmpty()
    {
        return size == 0;
    }
    T pop()
    {
        if (!isEmpty())
        {
            T temp = array[size];
            size--;
            return array[size];
        }
    }
    ~Stack()
    {
        delete[] array;
        array = nullptr;
    }
    T top()
    {
        if (!isEmpty())
        {
            return array[size - 1];
        }
        else
            cout << "Stack is Empty" << endl;
    }
    int get_size()
    {
        return size;
    }
    void Display()
    {
        if (!isEmpty())
        {
            for (int i = 0; i < size; i++)
            {
                cout << array[i] << " ";
            }
            cout << endl;
        }
        else
            cout << "Stack is Empty" << endl;
    }
    T *reGrow(T *array, int size, int &capacity)
    {
        T *tempArray = new T[capacity + 1];
        for (int i = 0; i < size; i++)
        {
            tempArray[i] = array[i];
        }
        capacity++;
        delete[] array;
        array = nullptr;
        return tempArray;
    }
};
class Tree
{
public:
    char val;
    Tree *right;
    Tree *left;
    Tree(int val){ this->val = val; right = nullptr; left = nullptr; }
    Tree(){ val = 0; right = left = nullptr; };
    ~Tree(){};
};
class TreeList
{
public:
    Tree *ptr;
    TreeList *next;
    TreeList(){};
    TreeList(Tree *treeNode) :ptr(treeNode), next(nullptr){};
    ~TreeList(){};
};
bool isOperator(char x)
{
    switch (x) {
    case '+':
    case '-':
    case '/':
    case '*':
        return true;
    }
    return false;
}

 

class StackExpressionTree
{
    TreeList *treeData;
public:
    StackExpressionTree(){ treeData = nullptr; };
    void push(Tree *ptr)
    {
        if (treeData == nullptr)
            treeData = new TreeList(ptr);
        else
        {
            TreeList *nptr = new TreeList(ptr);
            nptr->next = treeData;
            treeData = nptr;
        }
    }
    Tree *pop()
    {
        if (treeData != nullptr)
        {
            Tree *ptr = treeData->ptr;
            treeData = treeData->next;
            return ptr;
        }
        else
        {

 

        }
    }
    Tree *peek()
    {
        return treeData->ptr;
    }
    void insert(char symbol)
    {
        if (isCharacter(symbol))
        {
            Tree *ptr = new Tree(symbol);
            push(ptr);
        }
        else if(isOperator(symbol))
        {
            Tree *ptr = new Tree(symbol);
            ptr->left = pop();
            ptr->right = pop();
            push(ptr);
        }
         else
         {
             cout << "Invalid Expression" << endl;
             return;
         }
    }
    bool isCharacter(char ch)
    {
        return (ch >= 'a' && ch <= 'z')||(ch >='A' && ch<='Z');
    }
    bool isOperator(char ch)
    {
        return ch == '+' || ch == '-' || ch == '*' || ch == '/';
    }
    void buildTree(string expression)
    {
        for (int i = expression.length() - 1; i >= 0; i--)
            insert(expression[i]);
    }
    void inOrder()
    {
        inOrderRecurssion(peek());
    }
    void inOrderRecurssion(Tree *ptr)
    {
        if (ptr != nullptr)
        {
            inOrderRecurssion(ptr->left);
            cout << ptr->val;
            inOrderRecurssion(ptr->right);
        }
        return;
    }
    void preOrder()
    {
        preOrderRecurssion(peek());
    }
    void preOrderRecurssion(Tree *ptr)
    {
        if (ptr != nullptr)
        {
            cout << ptr->val;
            preOrderRecurssion(ptr->left);
            preOrderRecurssion(ptr->right);
        }
        return;
    }
};
int main()
{
    displayMenu();
    return 0;
}
void mainMenu(int dot)
{
    
    char temp;
    ifstream fin;
    int size = 0;
    char *arr = nullptr;
    fin.open("input.txt");
    while (!fin.eof())
    {
        fin >> temp;
        if (temp != ' ')
        {
            arr = reGrow(arr, size);
            arr[size++] = temp;
        }
    }
    fin.close();
    StackExpressionTree et;
    string data = PostfixToPrefix(arr, size);
    if (dot == 1)
    {
        cout << "\nPreOrder  : ";
        cout << data << endl;
    }
    else if (dot == 2)
    {
        et.buildTree(PostfixToPrefix(arr, size));
        cout << "\ninOrder  : ";
        et.inOrder();
    }
    delete[] arr;
}
int OperatorAndItsPrecedence(char temporary)
{
    if (temporary == '+' || temporary == '-')
        return 0;
    else if (temporary == '*' || temporary == '/')
        return 1;
    else if (temporary == '^')
        return 2;
    else if (temporary == ')' || temporary == '(')
        return 3;
    return 4;
}
string PostfixToPrefix(char * expression,int size)
{
    string infixExpression;
    Stack<string> tempStack;
    for (int i = 0; i < size; i++)
    {
        if (OperatorAndItsPrecedence(expression[i]) == 4)
        {
            string temp;
            temp += expression[i];
            tempStack.push(temp);
        }
        else
        {
            string one = tempStack.pop();
            string two = tempStack.pop();
            tempStack.push(expression[i] + two + one);
        }
    }
    while (!tempStack.isEmpty())
    {
        infixExpression += tempStack.pop();
    }
    return infixExpression;
}
char *reGrow(char *array, int size)
{
    char *tempArray = new char[size + 1];
    for (int i = 0; i < size; i++)
    {
        tempArray[i] = array[i];
    }
    tempArray[size] = '\n';
    delete[] array;
    array = nullptr;
    return tempArray;
}
void displayMenu()
{
    int dot = -99;
    while (dot != 0)
    {
        cout << "(Press 1) for inOrder printing the data." << endl;
        cout << "(Press 2) for preOrder printing the data." << endl << endl;
        cin >> dot;
        mainMenu(dot);
    }
}
