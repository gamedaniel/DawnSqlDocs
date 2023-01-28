# 合并K个升序链表
## 题目地址：
https://leetcode.cn/problems/merge-k-sorted-lists/
## 解题的思路：
比较有序序列第一个值，取最小的放到新的序列中，其余的递归即可。如果只有一个序列就直接附加到新的序列后面即可。

## 代码
```sql
function seq_sort(lst:list)
{
    innerFunction {
        function getMix(lst:list)
        {
            /**
            获取集合 lst 中第一个元素最小的值，并记录下集合的 index
            */
            let min_vs = null;
            for (index in range(count(lst)))
            {
                match {
                    null?(min_vs): min_vs = [lst.nth(index).first(), index];
                    lst.nth(index).first() < min_vs.get(0): min_vs = [lst.nth(index).first(), index];
                }
            }

            /**
            返回最小的值和重构的集合
            */
            let index = min_vs.get(1);
            let m = lst.get(index);
            match {
                empty?(m.rest()): lst.remove(index);
                notEmpty?(m.rest()): lst.set(index, m.rest());
            }
            [min_vs.first(), lst];
        }
    }
    let min_vs = getMix(lst);
    match {
        empty?(min_vs): [];
        min_vs.count() == 2 and empty?(min_vs.last()): [min_vs.first()];
        min_vs.count() == 2 and notEmpty?(min_vs.last()): match {
            min_vs.last().count() == 1: concat([min_vs.first()], min_vs.last().first());
            else concat([min_vs.first()], seq_sort(min_vs.last()));
        }
    }
}
seq_sort([[1,3,90], [2,4,10], [5,21]]);
```
## 执行结果

<img src='/smart_sql_img/seq_sort.jpg'></img><br/>

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
http://localhost:8080/serverless?user_token=wudafu_token_1&func=seq_sort&params=[[1,3,90],[2,4,10],[5,21]]<br/>
参数说明： <br/>
user_token: wudafu_token_1<br/>
func：seq_sort<br/>
params: [[1,3,90],[2,4,10],[5,21]] <br/>

## JDBC运行结果

<img src='/smart_sql_img/seq_sort_run.jpg'></img><br/>

## 要点

演示 innerFunction 的用法

