### 部署gitlab
启动容器
```
docker run \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --volume /u1/gitlab/config:/etc/gitlab \
    --volume /u1/gitlab/logs:/var/log/gitlab \
    --volume /u1/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce
```
#### 自定义标记labels，添加即可
```
vim /opt/gitlab/embedded/service/gitlab-rails/lib/gitlab/issues_labels.rb
```
```
          { title: "bug", color: red },
          { title: "critical", color: red },
          { title: "confirmed", color: red },
          { title: "documentation", color: yellow },
          { title: "support", color: yellow },
          { title: "discussion", color: blue },
          { title: "suggestion", color: blue },
          { title: "enhancement", color: green },
          ### 新增
          { title: "1.可提测", color: "#AD8D43", description: "需求开发完成，可以提测--开发人员打的标签" },
          { title: "2.代码初审", color: "#F0AD4E", description: "需求开发完成，由负责人初审通过--由指定代码审核人打的标签" },
          { title: "3.存在Bug", color: red, description: "需求测试未通过--测试人员打的标签" },
          { title: "4.已修复", color: yellow, description: "开发人员修复Bug，重新提交测试--开发人员打的标签 "},
          { title: "5.测试通过", color: "#8E44AD", description: "测试人员，验收完成--测试人员打的标签" },
          { title: "6.终审通过", color: "#7F8C8D", description: "负责人代码审核通过--代码审核人打的标签" },
          { title: "7.可合并", color: "#69D100", description: "代码测试验收完成，达到上线标准--项目管理员打的标签" },
          { title: "8.已集成", color: green, description: "已合并到release分支--代码管理打的标签" },
          { title: "9.已发布", color: blue, description: "已合并到master分支--代码管理打的标签" }
```

#### 然后重启就可以了
```
gitlab-ctl reconfigure
gitlab-ctl restart
```

