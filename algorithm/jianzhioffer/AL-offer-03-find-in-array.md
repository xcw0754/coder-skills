#二维数组中的查找

### Q：给一个n*m数组(即矩阵)，矩阵的每列从上到下有序，从左到右有序。给定一个数字，问它是否存在于此数组中？

| col1| col2| col3 | col4
|-----|-----|------|-----
|1    |2    |8     |9
|2    |4    |9     |12
|4    |7    |10    |13
|6    |8    |11    |15


> 分析一下，题比较简单，设游标(i,j)从下标[0][m]开始，仅有两条路可选
> - 向左走。当target<array[i][j]时需要向左。
> - 向下走。当target>array[i][j]时需要向右。
> - 找到了。当target=array[i][j]时。

参考代码？
```
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        if(array.empty())	return false;
        int i = 0, j = array[0].size() - 1;
        for(; i<array.size(); i++)
        {
            for(; j>=0; j--)	//向左
            {
                if(array[i][j]==target)	return true;
                if(array[i][j]<target)	break; //向下
            }
            if(j==-1)	return false;
        }
        return false;
    }
};
```