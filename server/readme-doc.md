
# 开发文档

> 基于django+mysql开发的宠物领养管理系统，本文档包括功能说明、数据库设计、开发过程。学习过程遇到问题可以咨询作者：lengqin1024（微信）

## 功能说明

- 宠物管理（包括宠物的新增、查询、删除、编辑功能）
- 分类管理（包括分类的新增、查询、删除、编辑功能）
- 领养管理（包括领养关系的新增、查询、删除、编辑功能）
- 用户管理（包括用户和管理员的新增、查询、删除、编辑、演示账号功能）
- 日志管理（包括日志的新增、查询、删除功能，通过日志查看系统访问情况）
- 统计分析（包括小区的人数统计、小区统计等功能）

## 数据库设计

```

// 宠物表
Table thing {
    thing_id int [pk]
    classification_id int [ref: > C.classification_id]
    tag_id int [ref: <> tag.tag_id]
    title varchar
    cover varchar
    price varchar
    address varchar //地区
    nickname varchar // 名字
    sex varchar // 性别
    status int // 上线0 下架1
    repertory int
    score varchar
    description text
    create_time datetime
    pv int
    wish_count int
    recommend_count int
    wish int [ref: <> user.user_id]
    collect int [ref: <> user.user_id]
 }
 
 // 分类表
 Table classification as C {
   classification_id int [pk]
   pid int
   title varchar
   create_time datetime
 }
 
 Table tag {
   tag_id int [pk]
   title varchar
   create_time datetime
 }
 
 Table comment {
   comment_id int [pk]
   content varchar
   user_id int [ref: > user.user_id]
   thing_id int [ref: > thing.thing_id]
   comment_time datetime
   like_count int
 }
 
 Table user {
   user_id int [pk]
   role varchar // 1管理员 2普通用户 3演示帐号
   status int // 0正常 1封号
   username varchar
   password varchar
   nickname varchar
   avatar varchar
   description varchar
   wish int [ref: <> thing.thing_id]
   email varchar
   mobile varchar
   score int // 积分
   push_email varchar // 推送邮箱
   push_switch int  // 推送开关
   token varchar
   admin_token varchar
 }
 
 
 
 Table record {
   record_id int [pk]
   user_id int [ref: > user.user_id]
   classification_id int [ref: > C.classification_id]
   thing_id int [ref: > thing.thing_id]
   title varchar
   record_time varchar
 }
 
 
 Table login_log {
   log_id int [pk]
   username varchar
   ip varchar
   log_time datetime
 }
 
 // 操作日志
 Table op_log {
   id int [pk]
   re_ip varchar
   re_time datetime
   re_url varchar
   re_method varchar
   re_content varchar
   access_time varchar
 }
 
 // 异常日志
 Table error_log {
   id int [pk]
   ip varchar
   method varchar
   content varchar
   log_time varchar
 }
 
 // 收养表
 Table order {
   order_id int [pk]
   user_id int [ref: > user.user_id]
   thing_id int [ref: > thing.thing_id]
   status varchar  
   create_time datetime
   pay_time datetime // 支付时间
   receiver_name varchar // 收货人姓名
   receiver_address varchar // 地址
   receiver_phone varchar // 收货人电话
   remark varchar // 备注
 }
 
 
 
 Table banner {
   id int [pk]
   image varchar
   thing_id int [ref: > thing.thing_id]
   create_time datetime
 }
 
 
 
 Table ad {
   id int [pk]
   image varchar
   link varchar
   create_time datetime
 }
 
 
 
 Table notice {
   id int [pk]
   content varchar
   create_time datetime
 }
 
 
  Table address {
   address_id int [pk]
   user_id int [ref: > user.user_id]
   address_content varchar
   default int  // 是否默认地址
   create_time datetime
 }
 
 
 
```

## 开发过程

后端功能的开发使用java和sprintboot技术。
关于springboot的学习，可以参考[这里](https://springdoc.cn/spring-boot/)。

无论是用户管理、分类管理、收养管理等功能都是基于java的springboot框架开发的，开发流程是：
- 第一步：编写实体
- 第二步：编写mapper模型
- 第二步：编写service
- 第三步：编写controller


下面用宠物管理功能来演绎这个流程，其它的管理功能都是这个流程


**宠物管理功能开发**

1. 编写实体类

在entity文件夹中新建实体，然后编写模型代码。如下：
```

@Data
@TableName("b_thing")
public class Thing implements Serializable {
    @TableId(value = "id",type = IdType.AUTO)
    public Long id;
    @TableField
    public String title;
    @TableField
    public String nickname;
    @TableField
    public String sex;
    @TableField
    public String address;
    @TableField
    public String cover;
    @TableField
    public String description;
    @TableField
    public String price;
    @TableField
    public String status;
    @TableField
    public String createTime;
    @TableField
    public String score;
    @TableField
    public String pv;
    @TableField
    public String recommendCount;
    @TableField
    public String wishCount;
    @TableField
    public String collectCount;
    @TableField
    public Long classificationId;

    @TableField(exist = false)
    public List<Long> tags; // 标签

    @TableField(exist = false)
    public MultipartFile imageFile;

}

```

2. 编写mapper

在mapper中新增ThingMapper.xml并写入：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.gk.study.mapper.ThingMapper">
    <select id="getList" resultType="com.gk.study.entity.Thing">
        select * from b_thing;
    </select>
</mapper>

```


3. 编写service层代码

在service中新建ThingService文件，然后编写增删改查的代码。如下所示：
```
package com.gk.study.service;


import com.gk.study.entity.Thing;

import java.util.List;

public interface ThingService {
    List<Thing> getThingList(String keyword, String sort, String c, String tag);
    void createThing(Thing thing);
    void deleteThing(String id);

    void updateThing(Thing thing);

    Thing getThingById(String id);

    void addWishCount(String thingId);

    void addCollectCount(String thingId);
}

```

4. 编写controller代码

在controller中新建ThingController.java编写业务代码即可。如下
```
package com.gk.study.controller;

@RestController
@RequestMapping("/thing")
public class ThingController {

    private final static Logger logger = LoggerFactory.getLogger(ThingController.class);

    @Autowired
    ThingService service;

    @Value("${File.uploadPath}")
    private String uploadPath;

    @RequestMapping(value = "/list", method = RequestMethod.GET)
    public APIResponse list(String keyword, String sort, String c, String tag){
        List<Thing> list =  service.getThingList(keyword, sort, c, tag);

        return new APIResponse(ResponeCode.SUCCESS, "查询成功", list);
    }

    @RequestMapping(value = "/detail", method = RequestMethod.GET)
    public APIResponse detail(String id){
        Thing thing =  service.getThingById(id);

        return new APIResponse(ResponeCode.SUCCESS, "查询成功", thing);
    }

    @Access(level = AccessLevel.ADMIN)
    @RequestMapping(value = "/create", method = RequestMethod.POST)
    @Transactional
    public APIResponse create(Thing thing) throws IOException {
        String url = saveThing(thing);
        if(!StringUtils.isEmpty(url)) {
            thing.cover = url;
        }

        service.createThing(thing);
        return new APIResponse(ResponeCode.SUCCESS, "创建成功");
    }

    @Access(level = AccessLevel.ADMIN)
    @RequestMapping(value = "/delete", method = RequestMethod.POST)
    public APIResponse delete(String ids){
        System.out.println("ids===" + ids);
        // 批量删除
        String[] arr = ids.split(",");
        for (String id : arr) {
            service.deleteThing(id);
        }
        return new APIResponse(ResponeCode.SUCCESS, "删除成功");
    }

    @Access(level = AccessLevel.ADMIN)
    @RequestMapping(value = "/update", method = RequestMethod.POST)
    @Transactional
    public APIResponse update(Thing thing) throws IOException {
        System.out.println(thing);
        String url = saveThing(thing);
        if(!StringUtils.isEmpty(url)) {
            thing.cover = url;
        }

        service.updateThing(thing);
        return new APIResponse(ResponeCode.SUCCESS, "更新成功");
    }

    public String saveThing(Thing thing) throws IOException {
        MultipartFile file = thing.getImageFile();
        String newFileName = null;
        if(file !=null && !file.isEmpty()) {

            // 存文件
            String oldFileName = file.getOriginalFilename();
            String randomStr = UUID.randomUUID().toString();
            newFileName = randomStr + oldFileName.substring(oldFileName.lastIndexOf("."));
            String filePath = uploadPath + File.separator + "image" + File.separator + newFileName;
            File destFile = new File(filePath);
            if(!destFile.getParentFile().exists()){
                destFile.getParentFile().mkdirs();
            }
            file.transferTo(destFile);
        }
        if(!StringUtils.isEmpty(newFileName)) {
            thing.cover = newFileName;
        }
        return newFileName;
    }

}

```

