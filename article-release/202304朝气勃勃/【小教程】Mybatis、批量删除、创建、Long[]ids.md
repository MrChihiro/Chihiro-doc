# 【小教程】5步调用 Mybatis 批量删除 创建 Long[] ids
## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]

## 简介

- 在写测试类删除一些数据中，我们会使用到mybatis的批量删除。
- 教程会分享在实践中如何使用mybatis的批量删除。

## 实现步骤

1. 创建一个数组，可以是 Set集合 也可以是 list集合。
2. 查询业务，获取到所有数据,谨慎操作，这里方便演示数全查，全删了，实际请根据自身情况使用，正式数据库请删除业务前请备份好数据库。
3. 循环出id加入到集合中。
4. 将 Set/list数组转成 Long[]。
5. 调用批量删除方法。


```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class GenWord {
    @Autowired
    private IEnglishDictionariesWordService iEnglishDictionariesWordService;
    /**
     * @throws Exception
     *
     * Mybatis 批量删除 Long[] ids 创建
     *
     */
    @Test
    public void testDeleteAll() throws Exception {
        // 创建一个数组，可以是 Set集合 也可以是 list集合
//        HashSet<Long> longs = new HashSet<>();
        ArrayList<Long> longsList = new ArrayList<>();
        // 查询业务，获取到所有数据,谨慎操作，这里方便演示数全查，全删了，实际请根据自身情况使用，正式数据库请删除业务前请备份好数据库
        List<EnglishDictionariesWord> englishDictionariesWord = iEnglishDictionariesWordService.selectEnglishDictionariesWordList(new EnglishDictionariesWord());
        // 循环出id加入到集合中
        for (EnglishDictionariesWord item : englishDictionariesWord){
            longsList.add(item.getId());
        }
        // 将 Set/list数组转成 Long[]
        Long[] ids = longsList.toArray(new Long[]{});
        for (Long item : ids) {
            System.out.println("打印数组："+ item);
        }
        // 调用批量删除方法
        iEnglishDictionariesWordService.deleteEnglishDictionariesWordByIds(ids);
    }
}
```