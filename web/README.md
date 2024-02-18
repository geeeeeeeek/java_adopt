### 学习文档


#### 部署步骤

1. 修改constants.ts中的BASE_URL
2. vite build
3. 将dist部署到nginx


#### 配置解释

1. env.development 开发环境配置
2. eslintrc.js 代码规范化提示
3. vite.config.js vite 开发服务器配置



### 开发步骤

共分三步：开发接口、开发view、开发路由

第一步：开发接口

在api文件夹下，新建请求文件，然后写入api请求代码，如下

```
import { get, post } from '/@/utils/http/axios';

enum URL {
  list = '/myapp/admin/notice/list',
  create = '/myapp/admin/notice/create',
  update = '/myapp/admin/notice/update',
  delete = '/myapp/admin/notice/delete',
}

const listApi = async (params: any) => get<any>({ url: URL.list, params: params, data: {}, headers: {} });
const createApi = async (data: any) =>
  post<any>({
    url: URL.create,
    params: {},
    data: data,
    headers: { 'Content-Type': 'multipart/form-data;charset=utf-8' },
  });
const updateApi = async (params: any, data: any) =>
  post<any>({
    url: URL.update,
    params: params,
    data: data,
    headers: { 'Content-Type': 'multipart/form-data;charset=utf-8' },
  });
const deleteApi = async (params: any) => post<any>({ url: URL.delete, params: params, headers: {} });

export { listApi, createApi, updateApi, deleteApi };

```

第二步：开发view页面

在view文件夹下，新增页面vue文件，然后写入页面代码，比如user.vue

第三步：设置路由

在router的root.js文件里面配置路由地址。如下

```
      { path: 'overview', name: 'overview', component: () => import('/@/views/overview.vue') },
      { path: 'asset', name: 'asset', component: () => import('/@/views/asset.vue') },
      { path: 'thing', name: 'thing', component: () => import('/@/views/thing.vue') },
      { path: 'comment', name: 'comment', component: () => import('/@/views/comment.vue') },
      { path: 'user', name: 'user', component: () => import('/@/views/user.vue') },
```

即可

#### 常见问题

##### 变量
https://blog.csdn.net/qq_41636947/article/details/117907448

##### antd的css引入方式
在index.html里面引入的cdn

##### cdn
https://cdn.jsdelivr.net/npm/ant-design-vue@3.2.20/dist/
https://cdn.staticfile.org/ant-design-vue/3.2.20/antd.min.css

#### public文件夹内容在build后会自动打到dist中
