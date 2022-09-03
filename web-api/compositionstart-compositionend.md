# compositionstart和 compositionend

当文本段落的组成完成或取消时，compositionend 事件将被触发 (具有特殊字符的触发，需要一系列键和其他输入，如语音识别或移动中的字词建议)。

input事件没办法知道我们在使用中文输入法，一旦输入就修改filterText。那怎么办呢？

使用这两个API可以解决中文输入的问题。比如当我们输入字母的时候，不让起触发真正绑定的input回调函数。

因为任何输入都会触发input，而输入中文的时候才触发`compositionstart`和`compositionend`，所以通过用一个标记`lock`来避免中文输入过程中触发过滤。

```vue
<template>
  <div id="app">
    <input
      type="text"
      :value="filterText"
      @input="onInput"
      @compositionstart="onCompositionStart"
      @compositionend="onCompositionEnd"
    />
    <ul>
      <li v-for="item in filteredList" :key="item">
        {{ item }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      filterText: "",
      list: [
        "The Wealth of Nations",
        "战争与和平",
        "海底两万里",
        "三国演义",
        "嫌疑人X的献身"
      ],
      lock: false
    };
  },
  computed: {
    filteredList() {
      if (!this.filterText) {
        return this.list;
      }
      return this.list.filter(item => item.indexOf(this.filterText) > -1);
    }
  },
  methods: {
    onInput(e) {
      if (!this.lock) {
        this.filterText = e.target.value;
      }
    },
    onCompositionStart() {
      this.lock = true;
    },
    onCompositionEnd(e) {
      // 拼接上新输入的中文
      this.filterText += e.data;
      this.lock = false;
    }
  }
};
</script>
```



