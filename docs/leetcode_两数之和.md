# 两数之和
## 题目地址：
https://leetcode.cn/problems/two-sum/
## 解题的思路：
将数字序列中的值，保存在字典中，值为 key，下标为值。
这样就转变成查询问题了，只需要遍历数字序列，查询 target - 遍历的值是否在字典中，
就可以得出两个整数的下标。

## 代码
```sql
function twoSum(nums:list, target:int)
{
    let dic = {};
    let my_count = count(nums);
    let rs = [];

    for (i in range(my_count))
    {
        match {
            dic.contains?(target - nums.nth(i)): rs.add([i, dic.get(target - nums.nth(i))]);
        }
        dic.put(nums.nth(i), i);
    }
    rs;
}
--twoSum([2,7,11,15], 9);
--twoSum([3,2,4], 6);
twoSum([3,3], 6);
```

## 执行结果

<img src='/smart_sql_img/two_sum.jpg'></img><br/>

## 在 JDBC 中执行
```java
    @RequestMapping(value = "/serverless")
    public @ResponseBody
    String serverless(ModelMap model, HttpServletRequest request, HttpServletResponse response,
                      @RequestParam(value = "user_token", required = false) String user_token,
                      @RequestParam(value = "func", required = false) String func,
                      @RequestParam(value = "params", required = false) String params) throws SQLException, ClassNotFoundException {

        Class.forName("org.apache.ignite.IgniteJdbcDriver");
        String url = "jdbc:ignite:thin://127.0.0.1:10800/public?lazy=true&userToken=" + user_token;
        Connection conn = DriverManager.getConnection(url);

        String funcLine = func + "(" + params + ")";

        Object ob = null;
        PreparedStatement stmt = conn.prepareStatement(funcLine);
        ResultSet rs = stmt.executeQuery();

        while (rs.next()) {
            ob = rs.getObject(1);
        }
        rs.close();
        stmt.close();
        conn.close();
        return ob.toString();
    }
}
```
在浏览器中输入：
http://localhost:8080/serverless?user_token=wudafu_token_1&func=twosum&params=[2,7,11,15],9 <br/>
参数说明： <br/>
user_token: wudafu_token_1<br/>
func：twosum<br/>
params: [2,7,11,15],9 <br/>
## JDBC运行结果

<img src='/smart_sql_img/two_sum_jdbc.jpg'></img><br/>

## 要点
1. count 返回序列大小
2. range 生成序列
3. 演示了基本的 for 和 match 的用法
具体用法：<a href='https://docs.dawnsql.com/#/DawnSql%E8%AF%AD%E8%A8%80/DawnSql%E4%B8%AD%E7%9A%84%E5%87%BD%E6%95%B0?id=_91%e3%80%81%e6%93%8d%e4%bd%9c%e9%9b%86%e5%90%88%e7%9a%84%e6%96%b9%e6%b3%95'>DawnSql中的函数</a>


































