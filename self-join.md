```
SELECT w1.id
FROM Weather w1
JOIN Weather w2 
    ON DATEDIFF(w1.recordDate, w2.recordDate) = 1
WHERE w1.temperature > w2.temperature;
```
## 如何定义“昨天”？ (DATEDIFF)

这是本题的技术核心。简单的 w1.id = w2.id + 1 是不可靠的，因为 id 可能不连续，或者日期之间有间隔。

`DATEDIFF(date1, date2)`：计算两个日期之间的天数差（date1 - date2）。

`DATEDIFF(w1.recordDate, w2.recordDate) = 1` 确保了 w1 恰好是 w2 的后一天。
