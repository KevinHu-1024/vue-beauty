<template lang="html">

  <div>

    <section class="markdown">
      <h1>CodeBox 代码示例</h1>
      <p>
        用于展示组件代码。
      </p>
      <h2>何时使用</h2>
      <ul>
        <p>
          组件说明文档的代码展示。
        </p>
      </ul>
      <h2>组件演示</h2>
    </section>

    <div class="ant-row" style="margin-left: -8px; margin-right: -8px;">

      <div class="ant-col-lg-24 code-boxes-col-2-1">

        <code-box
          title="基本"
          describe="基本用法"
        >
          <code-box
            title="基本"
            describe="button基本用法"
          >
            <v-button>Default</v-button>
          </code-box>
        </code-box>

        <code-box
          title="code"
          describe="自定义示例代码"
        >
          <code-box
            title="基本"
            describe="button基本用法"
            code="自定义<v-button>Default</v-button>自定义"
          >
            <v-button>Default</v-button>
          </code-box>
        </code-box>

      </div>

    </div>


    <api-table
      :content='content'
    ></api-table>

  </div>

</template>

<script>
import codeBox from '../components/codeBox'
import vButton from '../../components/button'
import apiTable from '../components/apiTable'

export default {
  data: function () {
    return {
      content: [
        [
          'title',
          '标题',
          'string',
          '无'
        ],
        [
          'describe',
          '对该组件的描述',
          'string',
          '无'
        ],
        [
          'slot::default',
          '组件的插槽，用于演示',
          'slot node',
          '无'
        ],
        [
          'code',
          '组件的示例代码，如果没有将直接使用slot::default的内容',
          'string',
          '无'
        ],
      ]
    }
  },
  components: {
    codeBox,
    vButton,
    apiTable
  }
}
</script>

<style lang="less">
</style>
