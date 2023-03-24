<script setup>
// 双向绑定实现

// weakMap 常用于存储只有当 key 所引用的对象存在时（没有被回收）才有价值的信息，十分贴合双向绑定的场景
const bucket = new WeakMap() // 存储副作用函数

let activeEffect // 被注册的函数

const tempObj = {} // 临时对象，用于操作

const data = { text: 'Hello Wrold' } // 相应数据

// 清除依赖
function cleanup (effectFn) {
  for (let i = 0; i < effectFn.deps.length; i++) {
    const deps = effectFn.deps[i]
    deps.delete(effectFn)
  }

  effectFn.deps.length = 0
}

// 处理依赖函数
function effect (fn) {
  const effectFn = () => {
    cleanup(effectFn)
    activeEffect = effectFn
    fn()
  }
  effectFn.deps = []
  effectFn()
}

// 追踪变化
function track (target, key) {
  if (!activeEffect) return

  let depsMap = bucket.get(target)

  if (!depsMap) bucket.set(target, (depsMap = new Map()))

  let deps = depsMap.get(key)
  if (!deps) depsMap.set(key, (deps = new Set()))

  deps.add(activeEffect)

  activeEffect.deps.push(deps)
}

// 触发变化
function trigger (target, key) {
  const depsMap = bucket.get(target)

  if (!depsMap) return

  const effects = depsMap.get(key)
  const effectsToRun = new Set(effects)

  effectsToRun.forEach(effectFn => effectFn())
}

const obj = new Proxy(data, {
  // 读取
  get (target, key) {
    if (!activeEffect) return
    console.log('get -> key', key)
    track(target, key)
    return target[key]
  },
  // 设置
  set (target, key, newValue) {
    console.log('set -> key: newValue', key, newValue)
    target[key] = newValue
    trigger(target, key)
  }
})

effect(() => {
  tempObj.text = obj.text
  console.log('tempObj.text :>>', tempObj.text)
})

setTimeout(() => {
  obj.text = 'hi Vue3'
}, 1000)

console.log(data)
</script>

<template></template>
