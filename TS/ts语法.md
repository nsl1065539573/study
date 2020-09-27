#####  1. 获取上下文

```typescript
	context.get(TrionesContext)
	// 例如 获取到商户id
	const tenantId = this.scene.get(TrionesContext).tenantId!;
```

#####  2. module.json

```typescript
// 这个是遇到了需要查询商户名称的bug
// 但是不知道如何查询 询问得到了load(Organization, 租户id)的答案，于是全局搜索 Organization
// 最终在module.json中找到了定义，里面声明了表结构
import { Organization } from '@app/Io/Tenant/Public/Organization/Organization';
const tenantId = this.scene.get(TrionesContext).tenantId;
return this.scene.load(Organization, {id: tenantId});
```

json文件如下：

```json
"Io/Tenant/Public/Organization": {
    "CreateOrganization": {
      "area": "Io/Tenant/Public/Organization",
      "name": "CreateOrganization",
      "archetype": "Runnable",
      "source": "Dummy",
      "kind": "流程",
      "properties": []
    },
    "Organization": {
      "area": "Io/Tenant/Public/Organization",
      "name": "Organization",
      "archetype": "ActiveRecord",
      "source": "OrganizationAdapter",
      "kind": "第三方数据",
      "properties": [
        {
          "name": "id",
          "type": "string",
          "optional": false,
          "writable": true,
          "insertable": true,
          "readable": true
        },
        {
          "name": "name",
          "type": "string",
          "optional": false,
          "writable": true,
          "insertable": true,
          "readable": true
        },.....
在这个文件夹下：Io/Tenant/Public/Organization  有这些文件
```



