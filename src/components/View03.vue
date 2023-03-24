<script setup>
import { reactive } from 'vue'

/**
 * ref 接收一个内部值，生成对应的响应式数据，
 * 该内部值挂载在 ref 对象的 value 属性上；
 * 该对象可以用于模板和 reactive。
 * 使用 ref 是为解决值类型在 setup、computed、合成函数等情况下的响应式丢失问题
 */
function ref (val) {
  const wrapper = { value: val }
  Object.defineProperty(wrapper, '__v_isRef', { value: true })
  return reactive(wrapper)
}

/**
 * toRef 为响应式对象 reactive 的一个属性创建对应的 ref
 * 且该方式创建的 ref 与原属性保持同步
 */
function toRef (obj, key) {
  const wrapper = {
    get value () {
      return obj[key]
    },
    set value (val) {
      obj[key] = val
    }
  }

  Object.defineProperty(wrapper, '__v_isRef', { value: true })
  return wrapper
}

/**
 * toRefs 将响应式对象转换成普通对象，对象的每个属性都是对应的 ref ，两者间保持同步
 * 使用 toRefs 进行对象结构
 */
function toRefs (obj) {
  const ret = {}
  for (const key in obj) {
    ret[key] = toRef(obj, key)
  }
  return ret
}

// 脱ref
function proxyRefs (target) {
  return new Proxy(target, {
    get (target, key, receiver) {
      const value = Reflect.get(target, key, receiver)
      return value.__v_isRef ? value.value : value
    },
    set (target, key, newValue, receiver) {
      const value = target[key]
      if (value.__v_isRef) {
        value.val = value
        return true
      }

      return Reflect.set(target, key, newValue, receiver)
    }
  })
}
</script>

<template></template>
