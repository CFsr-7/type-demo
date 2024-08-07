<template>
  <div class="header_index">
    <span class="button" @click="convert">转换</span>
    <p class="describe">描述:左侧区域为数据源,右侧区域为类型源</p>
  </div>
  <div class="indexView">
    <div class="code">
      <CodeMirror
          ref="data"
          :code="dataCode"
          @change="change"
      ></CodeMirror>
    </div>
    <div class="code">
      <CodeMirror
          ref="type"
          :code="typesCode"
          @change="changeTypes"
      ></CodeMirror>
    </div>
  </div>
</template>

<script setup lang="ts" >

  import {
    ref
  } from "vue";

  import
    CodeUtils
  from "@/SDK/Code.js"

  import
    TypeUtils
  from "@/SDK/TypeConvert.js"

  import
    NoticeUtils
  from "@/utils/notice/noticeUtils"

  import
    CodeMirror
  from "@/components/codeMirror/CodeMirror.vue"

  // refs
  const data = ref();
  const type = ref();
  // 参数源
  const dataCode = ref();
  const typesCode = ref();

  /*
  * 更新数据源
  * */
  const change = (value:string) => {
    dataCode.value = value
  }

  /*
  * 更新类型源
  * */
  const changeTypes = (value:string) => {
    typesCode.value = value
  }

  /**
   * 检测数据类型
   * @param _value
   */
  const _typePrototype = (_value:any) => {
    return Object.prototype.toString.call(_value).slice(1,-1);
  }


  /*
  * 判空
  * */
  const checkNull = () => {
    let _value;
    try {
        // console.log(JSON.parse(JSON.stringify(dataCode.value)))
      _value = eval(`(${dataCode.value})`)
    }catch (err) {
      _value = !isNaN(Number(dataCode.value)) ? Number(dataCode.value) : dataCode.value
    }

    if(
        _typePrototype(_value) === 'object Object' &&
        Object.keys(_value).length > 0
    ){
      return {
          _value,
          isTrue:true,
      };
    }else if(
        _typePrototype(_value) === 'object Array' &&
        _value.length > 0
    ){
      return {
          _value,
          isTrue:true,
      };
    }else if(_value) {
      return {
          _value,
          isTrue:true,
      };
    }
    NoticeUtils.W("转换数据源为空","参数");
    return {
      _value,
      isTrue:false,
    };
  }

  /*
  * 生成类型
  * */
  const convert = () => {
    const filed = 'type'
    let _data = checkNull();
    if(!_data.isTrue) return;
    // json Types
    let typeJson = CodeUtils._JS_LogicFormat(
        TypeUtils({
            content:_data._value,
            typeName:filed,
            arrConfig:{
                // 复杂数组深层类型对比
                checkIsTrue:true,
                interName:'infoType',
            },
            objConfig:{
                // 对象数据类型名称
                interName:filed,
                // 类型名称是否唯一值
                isTrueInters:new Set<string>(),
            },
      })
    )
    typesCode.value = "";

    typesCode.value = typeJson.replace(/\s*\?\s*/g, "?");
  }

</script>
<style scoped>
  @import "@/assets/less/index.less";
</style>
