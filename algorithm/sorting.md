# 排序算法

- 冒泡排序
1.比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2.对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3.针对所有的元素重复以上的步骤，除了最后一个。
4.持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
```javascript
Array.prototype.bubble_sort = function() {
	var i, j, temp;
	for (i = 0; i < this.length - 1; i++)
		for (j = 0; j < this.length - 1 - i; j++)
			if (this[j] > this[j + 1]) {
				temp = this[j];
				this[j] = this[j + 1];
				this[j + 1] = temp;
			}
	return this;
};
var num = [22, 34, 3, 32, 82, 55, 89, 50, 37, 5, 64, 35, 9, 70];
num.bubble_sort();
for (var i = 0; i < num.length; i++)
	document.body.innerHTML += num[i] + " ";
```

- 快速排序 （分治后再递归）
1.从数列中挑出一个元素，称为“基准”（pivot），
2.重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分割结束之后，该基准就处于数列的中间位置。这个称为分割（partition）操作。
3.递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。
```javascript
Array.prototype.quickSort = function() {
    const l = this.length
    if(l < 2) return this
    const basic = this[0], left = [], right = []
    for(let i = 1; i < l; i++) {
      const iv = this[i]
      iv >= basic && right.push(iv) // to avoid repeatly element.
      iv < basic && left.push(iv)
    }
    return left.quickSort().concat(basic, right.quickSort())
}
const arr = [5, 3, 7, 4, 1, 9, 8, 6, 2];
const ascendArr = arr.quickSort()
```

- 简单选择排序
1.首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置
2.然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾
3.以此类推，直到所有元素均排序完毕。
```javascript
Array.prototype.selection_sort = function() {
  var i, j, min;
  var temp;
  for (i = 0; i < this.length-1; i++) {
    min = i;
    for (j = i + 1; j < this.length; j++) {
      if (this[min] > this[j]) {
        min = j;
      }
    }

    temp = this[min];
    this[min] = this[i];
    this[i] = temp;
  }
  return this;
}
```
- 简单插入排序

- 希尔排序


- 堆排序


- 二路归并排序

- 多路归并排序



- 计数排序

- 桶排序

- 基数排序


