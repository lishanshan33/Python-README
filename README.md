# Python-README
### Python期末Flask——交互可视化技术文档总结说明

##### 代码Github URL：https://github.com/lishanshan33/python-flask_web
##### pythonanywhere URL：http://lss123.pythonanywhere.com/


### 自我描述
该项目一共包含三个页面，我主要负责第二个功能的交互页面：[你的城市重视教育吗？](http://lss123.pythonanywhere.com/jiaohu_2)这个页面通过下拉框选择不同的省份，点击查询，会出现图与表反应该省份历年教育支出的情况。其余的两个页面分别为：[数据故事](http://lss123.pythonanywhere.com/jiaohu/)，[别的国家生的多吗？](http://lss123.pythonanywhere.com/jiaohu_1)整个Flask的大框架是由我与另一个Python小伙伴许钰然同学共同完成。


### HTML档描述
- 利用[Phantom](https://html5up.net/phantom) 主题模板进行重构
- default.html模板由navigation.html、scripts.html、footer.html进行嵌套渲染，采用jinja2标准进行嵌套，并设定变量content进行主要任务传参
- 主页由default.html与index.html构成
- 功能页为jiaohu.html、jiaohu_1.html、jiaohu_2.html渲染生成
- 数据故事为jiaohu.html与word_1.html~word_6.html六个图表html进行嵌套渲染。

### Python档描述
- app.py为主要路由函数设置
- app.py
- new_pyecharts.py为pyecharts函数的设置，将pyecharts模块加上本身数据进行封装，供功能页面多次调用
- 
### Web App动作描述
- web App主要实现列表循环实现下拉框表单的选取与数据的传递
- 设定主页，可选择单一功能页面进行数据图表的查看，也可对于数据故事进行查看后，在数据故事页面进行数据的选择与图表的交互
- 实现在同一页面中，两个from表单共存提交：通过将request模块的值进行字典化，通过选择判断提交的表单为哪个功能表单，进行if判断后返回所需的功能页面

### 数据交互
- 数据结构的循环（列表循环、字典循环、集合循环)
利用jinja2进行选择下拉表单的列表循环

- 合适的推导式
 new_pyecharts.py中overlap_line_scatter3()函数中将列表数据转换成int格式
 ```python
 def overlap_line_scatter3(city) -> Bar:
    city_index = list(df5["地区"]).index("{}".format(city))
    fxjy = list(df5.iloc[city_index].to_dict().values())[1:][::-1]
    fxjy = [int(fxjy[i]) for i in range(len(fxjy))]
    bar = (
        Bar(opts.InitOpts(width='1150px', height='550px',
                          theme=ThemeType.LIGHT))  # opts.InitOpts(width = '810px',height = '500px')
            .add_xaxis(list(df5.columns)[1:][::-1])
            .add_yaxis("教育经费支出", fxjy)
            .set_series_opts(label_opts=opts.LabelOpts(is_show=False))
            # .add_yaxis)("商家B", Faker.values()
            .set_global_opts(title_opts=opts.TitleOpts(title="{}省教育经费情况".format(city), subtitle="",
                                                       subtitle_textstyle_opts=opts.TextStyleOpts(color="red",
                                                                                                  font_size=14,
                                                                                                  font_style="italic")),
                             legend_opts=opts.LegendOpts(pos_right="15%")
                             )

    )
    line = (
        Line()
            .add_xaxis(list(df5.columns)[1:][::-1])
            .add_yaxis("教育经费支出", fxjy)
            .set_series_opts(label_opts=opts.LabelOpts(is_show=False))

    )
    bar.overlap(line)
    return bar.render('templates/word_5.html')
 ```
- 适当的条件判断
 在app.py中利用if判断将request模块的值进行字典化，通过选择判断提交的表单为哪个功能表单，进行if判断后返回所需的功能页面
 ```python
 @app.route('/jiaohu/1',methods=['POST'])
def jiaohu1():
    the_select_region = list(df["Country Name"])
    the_select_region_1 = list(df5["地区"])
    page_title = '交互式可视化数据故事'
    page_description = '这是python与交互式可视化自我策展成果'
    print(list(request.values.to_dict().keys())[0])
    if list(request.values.to_dict().keys())[0] == "the_region_selected":
        the_region = request.form["the_region_selected"]
        overlap_line_scatter(the_region)
        table_1 = pd.DataFrame(df.iloc[list(df["Country Name"]).index("{}".format(the_region))]).T.to_html()
        with open("templates/word_2.html", encoding="utf8", mode="r") as f:
            plot_all = "".join(f.readlines())
        return render_template('default.html',
                               title=page_title,
                               description=page_description,
                               content=render_template('jiaohu_1.html',
                                                       word_2=plot_all,
                                                       table_1=table_1,
                                                       the_select_region=the_select_region,
                                                       ))
    else:
        the_region_1 = request.form["the_region_selected_1"]
        overlap_line_scatter3(the_region_1)
        table_2 = pd.DataFrame(df5.iloc[list(df5["地区"]).index("{}".format(the_region_1))]).T.to_html()
        with open("templates/word_5.html", encoding="utf8", mode="r") as f1:
            plot_all_1 = "".join(f1.readlines())
        return render_template('default.html',
                               title=page_title,
                               description=page_description,
                               content=render_template('jiaohu_2.html',
                                                        word_5=plot_all_1,
                                                       table_2 = table_2,
                                                       the_select_region_1=the_select_region_1))
 ```

### 自定义函数与模块功能
- 利用pyecharts进行图表的函数定义

<br />

##### 进行本地运行

1.克隆我的项目
  ```
  $ git clone 
  $ cd app_web
  ```

2. 用virtualenv进行虚拟化并激活:
  ```
  $ virtualenv --no-site-packages env
  $ source env/bin/activate
  ```

3. 下载所需要的文件:
  ```
  $ pip install -r requirements.txt
  ```

5. 运行项目:
  ```
  $ python app.py
  ```

6. 访问本地连接 [http://localhost:5000](http://localhost:5000)


<br />

