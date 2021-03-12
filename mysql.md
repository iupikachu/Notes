### mysql 

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201105095348994.png" alt="image-20201105095348994" style="zoom:50%;" />

添加外键约束



```mysql
ALTER TABLE `student` ADD CONSTRAINT `FK_geadeid` FOREIGN KEY(`gradeid`) REFERENCES `grade`(`gradeid`);
```

