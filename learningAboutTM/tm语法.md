

####  1. 标签中添加attr为组件属性赋值

```html
<!-- 给StorefrontNavBar这个组件的hasBcak属性赋值为false -->
<StorefrontNavBar :title="title">
        <attr #hasBack>false</attr>
</StorefrontNavBar>
```

####  2. 关于标签中的属性前跟 ":" 或者跟 "@"

```html
<!-- 
	标签前跟":" 表示这是对应的一个值，在ts界面中
	标签前跟"@" 表示后面跟的是一个方法，如常规的@onClick
-->
 <button @onClick="onSave">Save</button>
 <dynamic :visible="visitedControl">
     <span>收货地址</span>
 </dynamic>
```

对应ts：

```typescript
import * as Biz from '@triones/biz-kernel';
import { instantiate } from '@triones/tri-package';
import { ReactHost } from '@app/React/Host/ReactHost';
import { SaveCounter } from '@app/Scenario1/Private/SaveCounter';

@instantiate(ReactHost, { area: 'Scenario1/Ui' })
export class CounterForm extends Biz.MarkupView {
    
    public name: string;
    public value: number = 0;
    public visitedControl: boolean = true;

    public onSave() {
        this.call(SaveCounter, this);
    }
}
```

####  3. 关于dynamic里面的 :slot="some value"

```html
<!-- 其相当于一个switch 在dynamic里面的子标签内有一个  #some value  相当于switch里面的case -->
<!-- 
	下面这个例子 相当于
	如果  navBarVisible == true 那么显示 fragment #true 
	否则  显示 fragment #false
-->
<dynamic :slot="navBarVisible">
                <fragment #true>
                    <StorefrontNavBar :title="title">
                        <attr #hasBack>false</attr>
                    </StorefrontNavBar>
                    <StoreMenuBar />
                </fragment>
                <fragment #false>
                    <StoreRouteWrapper />
                </fragment>
            </dynamic>
```

