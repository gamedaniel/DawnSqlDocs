#  Dawn Sql 的设计对应用架构的支持

DawnSql 是一个超融合的，新函数式理念，它要实现的功能相当于分布式(mysql/pg) + 大数据体系 + 分布式缓存 + 微服务应用程序。

## Dawn Sql 的设置

### 1、设置是否多用户组

<img src='/smart_sql_img/multiUserGroup.jpg'></img>

设置多用户组为 true 后，就可以设置不同的 schema。schema 是数据表、no sql 和对应方法的集合，也就是说数据集包含了一个或多个表，no sql 和方法。一个用户组只属于一个 schema。另外还有一个 public schema为公共数据 schema。
*schema 可以理解为一个子系统的数据库*

<p style="font-size: larger;font-weight: bolder;">
1、 用户组默认只能访问，自己所在用户组的表、no sql 和方法。<br/>
2、 可以通过权限视图给用户组赋予，访问表的权限，可以通过内置函数 add_scenes_to 来给其它用户组赋予，访问方法的权限。<br/>
</p>

1. 对于 to B 的系统，通常业务的复杂度和变化都会比较大，根据业务的不同把业务划分到不同的数据集，这样有如下好处：通过分拆业务将一个非常复杂的业务分拆成几个简单的业务，方便系统的构建和维护。因为 Dawn Sql 支持业务的隔离性和共享性，使得构建这些业务变的非常简单。
2. 对于 SAAS 系统，有两个痛点，1、客户都定制化的需要。2、客户数据的安全性，既客户不愿意将自己的商业秘密数据放到 SAAS 平台。针对这两个痛点Dawn Sql 的解决方案是：1、将所有的公用功能都放到，public 数据集中，客户要定制化的需求，放到他们自己的数据集中。这样客户就可以自己来扩展 SAAS 平台的功能，同时不会影响到其它客户，如果其它客户需要为该客户提供服务，可以通过 add_scenes_to 将服务的权限授予该客户。2、客户可以通过加密的数据来和 SAAS 平台交互，当 SAAS 平台计算完成后，在调用客户的解密方法来解密数据，即使是 SAAS 平台也无法获知客户的隐私数据。
3. 对于 PAAS 系统，提供了一种新的 Dawn Sql 语言来描述业务，使得数据在分布式的数据库中，不需要移动，只需要将计算的脚本发到各个节点来计算，同时支持隐私计算，达到对数据"可用、不可见"的目的

**具体的操作**

**schema 相当于数据表的集合。当新建用户组的是，需要给它指定一个 schema。默认它可以读写同一个schema 中的所有表，当然也可以给它添加权限视图，让它的读写权限受到限制。如果一个用户组要读写其它 schema 中的表，就一定要设置权限视图！**

### 1.1、添加数据表的集合（schema）

```sql
-- 1、
-- 新增数据表的集合 myy
create schema if not exists myy;
-- 或者
create schema myy;

-- 2、
-- 新增数据表的集合 wudafu
create schema if not exists wudafu;
-- 或者
create schema wudafu;

-- 3、
-- 新增数据表的集合 wudagui
create schema if not exists wudagui;
-- 或者
create schema wudagui;
```

如果要删除数据表的集合

```sql
-- 1、
-- 删除数据表的集合 myy
drop schema if exists myy;
-- 或者
drop schema myy;

-- 2、
-- 删除数据集 wudafu
drop schema if exists wudafu;
-- 或者
drop schema wudafu;

-- 3、
-- 删除数据表的集合 wudagui
drop schema if exists wudagui;
-- 或者
drop schema wudagui;
```

### 1.2、添加用户组。(需要 root 权限)

```sql
add_user_group(group_name, user_token, group_type, data_set_name);
```

创建 schema 并为其添加用户组
```sql
create schema wudafu;
add_user_group('myy_group', 'myy_token', 'all', 'myy');

create schema wudafu;
add_user_group('wudafu_group', 'wudafu_token', 'all', 'wudafu');

create schema wudagui;
add_user_group('wudagui_group', 'wudagui_token', 'all', 'wudagui');
```

### 1.3、为用户组添加访问数据集和表的权限。(需要 root 权限)

```sql
-- 为用户组：wudafu_group，添加插入 public.Categories 表的权限。
-- 让其只能添加 CategoryName 和 Description 字段的数据
my_view('wudafu_group', 'INSERT INTO public.Categories(CategoryName, Description)');

-- 为用户组：wudafu_group，添加修改 public.Categories 表的权限。
-- 让其只能修改 CategoryName = 'Produce' 的 Description 字段
my_view('wudafu_group', "UPDATE public.Categories set Description where CategoryName = 'Produce'");

-- 为用户组：wudafu_group，添加删除 public.Categories 表的权限。
-- 让其只能删除 CategoryName <> 'Seafood' 的数据
my_view('wudafu_group', "DELETE from public.Categories where CategoryName <> 'Seafood'");

-- 为用户组：wudafu_group，添加查询 public.Categories 表的权限。
-- 让其只能查询 CategoryName <> 'Seafood' 且只能查询列 CategoryName, Description 的数据
my_view('wudafu_group', "SELECT CategoryName, Description from public.Categories where CategoryName <> 'Seafood'");

-- 为用户组：wudafu_group，添加查询 public.Categories 表的权限。
-- 让其只能查询 CategoryName <> 'Seafood' 且只能查询列 CategoryName, Description 的数据，并且将 CategoryName 的数据转换成 f(CategoryName) 
-- f 为函数
my_view('wudafu_group', "SELECT convert_to(CategoryName, f(CategoryName)), Description from public.Categories where CategoryName <> 'Seafood'");
```

### 1.4、为用户组删除访问数据集和表的权限。(需要 root 权限)

```sql
-- 删除用户组 wudafu_group 对表 public.Categories 插入的权限
rm_view('wudafu_group', 'public.Categories', 'insert');

-- 删除用户组 wudafu_group 对表 public.Categories 修改的权限
rm_view('wudafu_group', 'public.Categories', 'update');

-- 删除用户组 wudafu_group 对表 public.Categories 删除的权限
rm_view('wudafu_group', 'public.Categories', 'delete');

-- 删除用户组 wudafu_group 对表 public.Categories 查询的权限
rm_view('wudafu_group', 'public.Categories', 'select');
```

**用户组权限设置的原理：用户组读取或写数据的时候，sql 语句首先会被解析成语法树，在和用户操作该表的权限语法树合并成一个新的语法树**

### 2、不同用户组之间方法的调用
用户组可以调用自己、public、和被其它用户组授权的方法。
例如：
```sql
-- 用户组 A 定义了如下方法：
-- 输入一个字符串，返回一个前缀 "a:" 的字符串
function my_a(msg:string)
{
    concat("a:", msg);
}

-- 用户组 B 如果要使用用户组 A 中的方法，只需要让 root 用户执行 add_scenes_to 方法
-- 例如：将用户组 A 的 my_a 调用权限转移给，用户组 B，需要 root 用户先获取 A 的 ID 和 B 的 ID。
add_scenes_to(A的id, 'my_a', B的id);

-- 这样用户组 B 就可以使用 my_a 方法了

-- 删除 A 赋予 B 调用 my_a 的权限 (root 用户执行这个方法)
rm_scenes_from(A的id, 'get_data_set_id', B的id);
```

### 3、不设置多用户组

不设置用户组，所有创建的表都在 public 数据集中。不设置多用户组下面的功能就不能使用。

> 1、不能创建数据集
> 2、不能创建其它用户组
> 3、用户组都不存在了，也就没有办法为用户组设置权限


