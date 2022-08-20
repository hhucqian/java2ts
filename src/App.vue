<script setup lang="ts">
import { ref } from 'vue';

const java_code = ref("")
const ts_code = ref("")

function onTrans() {
  let code = java_code.value
  code = code.trim().replaceAll("public", "").replaceAll("private", "").replaceAll(/\s+@JsonIgnore\n.*/g, "")
  let code_lines = code.split("\n")
  code_lines = code_lines.map(line => {
    if (line.includes("class")) {
      return line.replace("class", "export interface")
    }
    if (line.endsWith(";")) {
      const parts = line.match(/(\s*)(\w+)(\[])?(\s*)(\w+);/)
      console.log(parts);
      if (parts) {
        const leading_space = parts[1]
        let param_type = parts[2]
        if (["int"].includes(param_type)) {
          param_type = "number"
        }
        if (["Date", "String"].includes(param_type)) {
          param_type = "string"
        }
        const param_arrya = parts[3] == undefined ? "" : parts[3]
        const param_name = parts[5]

        return `${leading_space}${param_name}: ${param_type}${param_arrya}`
      }
    }
    return line
  })


  ts_code.value = code_lines.join("\n")
}

</script>

<template>
  <div class="main">
    <div class="code">
      <textarea cols="120" rows="20" placeholder="JAVA 代码" v-model="java_code"></textarea>
    </div>
    <div class="code">
      <textarea cols="120" rows="20" placeholder="TypeScript 代码" v-model="ts_code"></textarea>
    </div>
    <div class="cmd">
      <button @click="onTrans">转换</button>
    </div>
  </div>
</template>

<style lang="scss" scoped>
</style>