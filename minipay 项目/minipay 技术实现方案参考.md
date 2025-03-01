

### 3.1.4.
加密
https://yuque.antfin.com/gmvwu4/qh9kiw/guu4cwgx41i37agt#JRUf8
线程池优化全表扫描
[4.3.2 线程池优化分库分表情况下的全库全表扫描](https://yuque.antfin.com/gmvwu4/qh9kiw/tq3w5l93gfoyfn8p#BYtx1)
https://yuque.antfin.com/gmvwu4/qh9kiw/frrigose5heked0f#C1Mgi
分库分表设计
https://yuque.antfin.com/gmvwu4/qh9kiw/gph6r9lx296g7qk7#o8g4W
动账通知
https://yuque.antfin.com/gmvwu4/qh9kiw/frrigose5heked0f#g7CAl
https://yuque.antfin.com/gmvwu4/qh9kiw/mr21gnpwcf6ptlsz#X7TK4
https://yuque.antfin.com/gmvwu4/qh9kiw/iwmm7gh7k1eni0kv#qzdoR
https://yuque.antfin.com/gmvwu4/qh9kiw/hdlmbztu3q31oouo#wGzeO
二次查询法
https://yuque.antfin.com/gmvwu4/qh9kiw/iwmm7gh7k1eni0kv#g8e7C
异步开户
https://yuque.antfin.com/gmvwu4/qh9kiw/iwmm7gh7k1eni0kv#GmPA6
https://yuque.antfin.com/gmvwu4/qh9kiw/guu4cwgx41i37agt#rm8d5

dtx转账
https://yuque.antfin.com/gmvwu4/qh9kiw/mr21gnpwcf6ptlsz#tyhJJ
https://yuque.antfin.com/gmvwu4/qh9kiw/hdlmbztu3q31oouo#zmdry
https://yuque.antfin.com/gmvwu4/qh9kiw/yv7b53dp6h1wsmhm#NhRuq
https://yuque.antfin.com/gmvwu4/qh9kiw/phu0a9ddccgk1skk#Q9jzH
https://yuque.antfin.com/gmvwu4/qh9kiw/phu0a9ddccgk1skk#tyhJJ
转账兜底
https://yuque.antfin.com/gmvwu4/qh9kiw/hdlmbztu3q31oouo#aapqN
对账
https://yuque.antfin.com/gmvwu4/qh9kiw/yv7b53dp6h1wsmhm#bELfy
https://yuque.antfin.com/gmvwu4/qh9kiw/frrigose5heked0f#bELfy
深分页
https://yuque.antfin.com/gmvwu4/qh9kiw/frrigose5heked0f#T6RCN
索引设计
https://yuque.antfin.com/gmvwu4/qh9kiw/frrigose5heked0f#ZRiJy

转账业务对事务的响应时间平均应当在300ms左右，最大响应时间是3000ms。

数据库采用公共数据库，对容量不进行分析。

吞吐量：预计转账吞吐量每秒大于60TPS，单条查询吞吐量大于300QPS


账务中心的数据热点主要在于转账业务时产生的账户余额变更和账户流水明细的增加，为了解决数据热点问题，我们对于热点用户采用**缓冲记账**和**缓冲补账**的方式代替一般的转账操作。

缓冲记账是针对某些余额变更请求频繁的账户（即热点用户），为了缓解大量交易量带来的性能下降，采用延时落账的方式代替实时落账。缓冲记账开启时，将这条流水明细记录在缓冲流水明细表中而不是账户流水明细表，同时，对于账户的资金余额也并不实时更改。通过之后的定时任务缓冲补账，来将这些流水表现于账户余额上。

本应用现阶段流量很少，不会出现上述问题，后期可以扩展实现缓冲记账功能。

在步骤4中，永远不要告诉用户输错的究竟是用户名还是密码。就像通用的提示那样，始终显示：“无效的用户名或密码。”就行了。这样可以防止攻击者在不知道密码的情况下枚举出有效的用户名。

为使攻击者无法构造包含所有可能盐值的查询表，盐值必须足够长。一个好的经验是使用和哈希函数输出的字符串等长的盐值。例如， SHA256 的输出为256位（32字节），所以该盐也应该是32个随机字节。


存储密码的步骤：

1. 使用 CSPRNG 生成足够长的随机盐值。
2. 将盐值混入密码，并使用标准的密码哈希函数进行加密，如Argon2、 bcrypt 、 scrypt 或 PBKDF2 。
3. 将盐值和对应的哈希值一起存入用户数据库。

校验密码的步骤：

4. 从数据库检索出用户的盐值和对应的哈希值。
5. 将盐值混入用户输入的密码，并且使用通用的哈希函数进行加密。
6. 比较上一步的结果，是否和数据库存储的哈希值相同。如果它们相同，则表明密码是正确的；否则，该密码错误。
加盐可以确保攻击者无法使用像查询表和彩虹表攻击那样对大量哈希值进行破解，但依然不能阻止他们使用字典攻击或暴力攻击。高端显卡（ GPU ）和定制的硬件每秒可以进行十亿次哈希计算，所以这些攻击还是很有效的。为了降低使这些攻击的效率，我们可以使用一个叫做密钥扩展（ key stretching）的技术。

这类算法采取安全因子或迭代次数作为参数。此值决定哈希函数将会如何缓慢。对于桌面软件或智能手机应用，确定这个参数的最佳方式是在设备上运行很短的性能基准测试，找到使哈希大约花费半秒的值。通过这种方式，程序可以尽可能保证安全而又不影响用户体验。


只要攻击者可以使用哈希来检查密码的猜测是对还是错，那么他们可以进行字典攻击或暴力攻击。下一步是将密钥（ secret key）添加到哈希加密，这样只有知道密钥的人才可以验证密码。有两种实现的方式，使用ASE算法对哈希值加密；或者使用密钥哈希算法 [HMAC](http://en.wikipedia.org/wiki/HMAC) 将密钥包含到哈希字符串中。

实现起来并没那么容易。这个密钥必须在任何情况下，即使系统因为漏洞被攻陷，也不能被攻击者获取。如果攻击者完全进入系统，密钥不管存储在何处，总能被找到。因此，密钥必须密钥必须被存储在外部系统，例如专用于密码验证一个物理上隔离的服务端，或者连接到服务端，例如一个特殊的硬件设备，如 [YubiHSM](https://www.yubico.com/YubiHSM) 。

我强烈建议所有大型服务（超过10万用户）使用这种方式。我认为对于任何超过100万用户的服务托管是非常有必要的。

大多数数据库被拖库是由于 [SQL 注入攻击](http://en.wikipedia.org/wiki/SQL_injection)，因此，不要给攻击者进入本地文件系统的权限（禁止数据库服务访问本地文件系统，如果有此功能的话）。

##### 我应该使用什么样的哈希算法？

可以使用：

- 精心设计的密钥扩展算法如 [PBKDF2](http://en.wikipedia.org/wiki/PBKDF2) 、[bcrypt](http://en.wikipedia.org/wiki/Bcrypt) 和 [scrypt](http://www.tarsnap.com/scrypt.html) 。
- OpenWall的的 [Portable PHP password hashing framework](http://www.openwall.com/phpass/)。
- [PBKDF2在PHP、C＃、Java和Ruby的实现](https://github.com/defuse/password-hashing)。
- [crypt](http://en.wikipedia.org/wiki/Crypt_%28Unix%29#Library_Function_crypt.283.29) 的安全版本。






# 二次查询性能比较
要分析这个 SQL 查询的时间复杂度，我们可以将其分解为几个主要步骤。根据你提供的 SQL 查询：

```sql
SELECT * FROM (
    SELECT id FROM tb00 WHERE id BETWEEN 3 AND 11 
    UNION 
    SELECT id FROM tb01 WHERE id BETWEEN 4 AND 12 
    UNION 
    SELECT id FROM tb03 WHERE id BETWEEN 22 AND 23 
) A 
ORDER BY id 
LIMIT 2,5;
```

### 分析步骤

1. **各个 `SELECT` 查询**：
   每个 `SELECT` 查询都有一个 `WHERE` 子句，它会通过对相应的 `id` 列进行索引查找。假设每个表的 `id` 列都有索引。

   - 查找时间复杂度为 O(log m)，其中 m 是表中的记录数。
   - 因此，对于每个表的查询，我们假设有 \( p \) 个表（在你的例子中假设为 3，但也考虑一般情况）。

   总的复杂度为：
   \[
   O(p \cdot \log m)
   \]
   其中 \( m \) 是每个表的记录数。

2. **合并结果集**：
   - 使用 `UNION` 会去除重复项，假设合并后的结果集的总行数为 \( k \)。
   - 合并操作（去重）通常的时间复杂度为 O(k)。

3. **排序操作**：
   - 对合并后的结果集进行排序，这一步的时间复杂度为 O(k log k)。

4. **LIMIT**：
   - LIMIT 子句用于从排序后的结果集中获取特定行，其对于限制操作的时间复杂度为 O(1)，因为它是在已排序的结果集上简单地跳过特定数量的项目。

### 总时间复杂度

将以上步骤结合起来，我们可以得到总的时间复杂度：

1. 查询阶段：\( O(p \cdot \log m) \)
2. 合并阶段：\( O(k) \)
3. 排序阶段：\( O(k \log k) \)
4. LIMIT 阶段：\( O(1) \)

所以总的时间复杂度可以表示为：
\[
O(p \cdot \log m + k + k \log k)
\]

### 简化

对于较大数据集:
- 如果 \( k \) 也取决于 \( m \)，形成 \( O(k) \) 和 \( O(k \log k) \) 是小的可能性。一般来说， 如果从每个表返回的数据量较大（例如，较接近总数据量），那么排序的影响可能会占据较大比重。

假设 \( p \) 是一个上界（例如不超过 100），在最坏情况下，查询和排序的复杂度可能成为主导因素，因此可以传达为：
\[
O(k \log k)
\]

### 结论

因此，整体的 SQL 查询的时间复杂度一般可以表示为 \( O(p \cdot \log m + k + k \log k) \)。在实际使用中，具体的常量影响，数据集的分布，及其具体的表结构将会影响查询的实际效率。



为了解决你的需求，即对一组数据进行排序，然后从排序后的结果中提取特定范围内的元素，可以使用 Java 的 `List` 接口实现。以下是一个具体的方案，包括排序和获取指定范围的数据。

### 实现步骤

1. **将数据存储在一个列表中**：使用 `ArrayList` 来存储数据。
2. **排序数据**：使用 `Collections.sort()` 方法对列表进行排序。
3. **提取指定范围的数据**：通过索引获取需要的元素。

### Java 实现代码

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class DataExtractor {

    public static List<Integer> extractRange(List<Integer> data, int start, int end) {
        // Step 1: Sort the data
        Collections.sort(data);

        // Step 2: Extract the specified range
        // We need values starting from index start to end
        // Adjust for 0-based index
        return data.subList(start - 1, end); // Start from start index to end index (1-based)
    }

    public static void main(String[] args) {
        // Example Data
        List<Integer> dataList = new ArrayList<>();
        dataList.add(19);
        dataList.add(11);
        dataList.add(3);
        dataList.add(5);
        dataList.add(1);
        dataList.add(9);
        dataList.add(7);
        dataList.add(13);
        dataList.add(15);
        dataList.add(17);
        
        // Specify the range (5-10 in 1-based indexing)
        List<Integer> result = extractRange(dataList, 5, 10);
        System.out.println("Extracted Range: " + result);
    }
}
```

### 说明
- **排序**：`Collections.sort(data)` 的时间复杂度为 O(n log n)，其中 n 是数据的大小。这是因为我们使用了 TimSort 算法来实现排序。
- **提取子列表**：`data.subList(start - 1, end)` 的时间复杂度为 O(k)，其中 k 是提取的元素数量。
- 所以整个 `extractRange` 方法的时间复杂度为 O(n log n + k)，其中 n 是列表的大小，k 是提取的元素数量。

### 选择最优方案的考虑
-  **排序算法**：在 Java 中，`Collections.sort()` 使用了 TimSort，是一种稳定且高效的排序算法，适用于大多数情况。因此，它的选择是合理的。
- **数据结构**：使用 `ArrayList` 存储数据是合理的，因为它支持动态数组，随机访问效率较高，适合在进行排序和子集提取时使用。
- **范围提取的灵活性**：使用 `subList` 方法可以更方便地提取数据，避免了额外的循环和空间消耗。

这种方案可以有效地处理你的需求，并通过 Java 的内置方法实现高效的排序和数据提取。



如果数据不变说明全局offset += 局部offset+1就是四个位置
如果有变化比如多了一条数据说明全局offset += 局部offset

表一之前10的offset = 4现在比之前多了一条数据求多了数据之后虚拟位的offset

我第一次查询一条数据范围根据下标3 - 8，而现在的数据重新查询，以某个值 - 现在的下标8，查询之后比之前多了一条即之前数据为y条，现在为y+1条，之前的范围是下标的3 - 8，求现在的位置是多少 - 8？
已知：
现在的数据比之前多一条，所以得出，现在数据左区间为之前数据的左区间-1即4-1=3在根据第二次查询是根据一个全局最小值 ~ 局部最大值，而这里的局部最大值不会变
所以一定是全局最小值<局部最小值，所以全局offset的位置为局部最小值-1即3-1=2。


再来一种情况：
第一次查询3 - 8
第二次查询还是3- 8
由于第一次查询是根据位置查询即4-9这个位置得知左区间为4，右区间为9
而第二次查询区间为全局最小 ~ 局部最大，发现俩次查询数据条数一模一样，即推理出数据没有变化过。
所以全局offset的位置为第一次查询的左区间位置即4
现在整理一下。。。。
计算galble-offset需要的步骤：
1. 第一次查询的数量first-total（已知）
2. 第二次的查询数量second-total（已知）
3. 计算局部游标local-offset（已知）
4. 计算局部最小值local-min（已知）
5. 计算俩次查询数据条数差diff-total
6. 求出虚拟游标virtual-offset
	1. 如果diff-total >0 :如果俩次查询条数有变化说明当前虚拟游标位置需要重新计算。
	2. 如果diff-total = 0：不需要重新计算，diff-total  = local-offset
这里说明俩个逻辑原因：
已知第一次查询是根据下标查询即4-9这个范围。
而第二次查询是感觉全局最小值 ~ 局部最大值即（9）这个范围。
如果条数有变化因为局部最大值是没变，所以只能是因为全局最小值<第一次查询的局部最小值（4）。
所以能推理出，因为全局最小值<第一次查询的就最小值则全局最小值的虚拟游标一定比局部最小值小，即local-offset<4；
那么局部虚拟游标具体下是多少呢？局部虚拟游标=local-min的下标位置即local-offset - diff-total ，完事之后左区间的下标位置在-1就能拿到局部虚拟offset位置。

offset 2 ，size 2
第一次
3 ，5
4， 6；
第二次
3，5
4，6
都不变，但是全局 offset为2，全局 offset = 小于这个 id 的记录数有多少。
123457678910
11，12，13，14，15，16，17，18，19，20
5，5
第一次
3，4，5，6，7。
13，15，16，17，18。
第二次
不变
不变
排序