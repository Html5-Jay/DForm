<script>
	export default {
		props: {
			option: {
				type: Object,
				default: () => {
					return {
						rules: {},
						errorType: 'message',
						borderBottom: true,
						labelPosition: 'top',
						labelWidth: '45',
						labelAlign: 'left',
						labelStyle: {},
            detail: true, // 是否详情页
						columns: []
					}
				}
			},
      // #ifdef VUE3
      modelValue: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // #endif
      // #ifdef VUE2
      value: {
        type: Object,
        default: () => {
          return {}
        }
      }
      // #endif
		},
		data() {
			return {
				form: {},
				currentItem: {}, // 当前操作的item
        ops: {},
				pickerConfig: {
					title: '',
					keyName: '',
					columns: [],
				}
			}
		},
    watch: {
      // #ifdef VUE3
      modelValue: {
        handler() {
          this.$nextTick(() => {
            this.form = {
              ...this.form,
              ...this.modelValue
            }
          })
        },
        immediate: true,
        deep: true
      },
      // #endif
      // #ifdef VUE2
      value: {
        handler() {
          this.$nextTick(() => {
            this.form = {
              ...this.form,
              ...this.value
            }
          })
        },
        immediate: true,
        deep: true
      }
      // #endif

    },
		mounted() {
			this.initForm()
		},
		computed: {
		},
		methods: {
      deepClone(obj, cache = new WeakMap()) {
        if (obj === null || typeof obj !== 'object') return obj;
        if (cache.has(obj)) return cache.get(obj);
        let clone;
        if (obj instanceof Date) {
          clone = new Date(obj.getTime());
        } else if (obj instanceof RegExp) {
          clone = new RegExp(obj);
        } else if (obj instanceof Map) {
          clone = new Map(Array.from(obj, ([key, value]) => [key, this.deepClone(value, cache)]));
        } else if (obj instanceof Set) {
          clone = new Set(Array.from(obj, value => this.deepClone(value, cache)));
        } else if (Array.isArray(obj)) {
          clone = obj.map(value => this.deepClone(value, cache));
        } else if (Object.prototype.toString.call(obj) === '[object Object]') {
          clone = Object.create(Object.getPrototypeOf(obj));
          cache.set(obj, clone);
          for (const [key, value] of Object.entries(obj)) {
            clone[key] = this.deepClone(value, cache);
          }
        } else {
          clone = Object.assign({}, obj);
        }
        cache.set(obj, clone);
        return clone;
      },

			formatTime(timestamp, formatType) {
				const date = new Date(timestamp);

				if (isNaN(date)) {
					return "Invalid Date";
				}

				let year = date.getFullYear();
				let month = String(date.getMonth() + 1).padStart(2, "0");
				let day = String(date.getDate()).padStart(2, "0");
				let hours = String(date.getHours()).padStart(2, "0");
				let minutes = String(date.getMinutes()).padStart(2, "0");
				let seconds = String(date.getSeconds()).padStart(2, "0");

				switch (formatType) {
					case "date":
						return `${year}-${month}-${day}`;
					case "time":
						return `${hours}:${minutes}`; //:${seconds}
					case "year-month":
						return `${year}-${month}`;
					case "datetime":
						return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
					default:
						return "Invalid Format Type";
				}
			},
			initForm() {
				// console.log(this.option.columns, 'initForm')
				const defaultForm = {}
				const rules = {}
        const _option = this.deepClone(this.option)
        const defaultOps = {
          errorType: 'message',
          borderBottom: true,
          labelPosition: 'top',
          labelWidth: '45',
          labelAlign: 'left',
          detail: false, // 是否详情页
          labelStyle: {},
        }
        const slots = this.$slots
        const slotsKeys = Object.keys(slots)
				// 初始化字段
        _option.columns.forEach(item => {
					if (item.prop) {
            if (['checkbox', 'file'].includes(item.type)) {
              defaultForm[item.prop] = []
            } else {
              defaultForm[item.prop] = ''
            }
					}
					if (item.rules) {
						rules[item.prop] = item.rules
            // 判断规则里是否有必填字段 并且非详情页
            if (item.rules && !_option.detail) {
              // 数组形式
              if (Object.prototype.toString.call(item.rules) === "[object Array]") {
                if (item.rules.some(rItem => rItem.required)) {
                  item.required = true
                }
              }
              if ( Object.prototype.toString.call(item.rules) === "[object Object]") {
                if (item.rules.required) {
                  item.required = true
                }
              }
            }
					}
          /**
           * 获取是否有插槽 目前文本框类型有前置 后置插槽
           * type设置为input 或者没有设置默认是文本
           */
          if (item.type === 'input' || !item.type) {
            slotsKeys.forEach(key => {
              if (key === `${item.prop}Prefix`) {
                item.isPrefix = true
                item.prefixName = key
              }
              if (key === `${item.prop}Suffix`) {
                item.isSuffix = true
                item.suffixName = key
              }
            })
          }
				})
				let form = Object.assign(defaultForm, this.value)
				this.$set(this, 'form', form)
        this.$set(this, 'ops', Object.assign(defaultOps, _option))
				setTimeout(() => {
					this.$refs.form.setRules(rules)
				}, 20)
			},
			selectValue(value, list, keyName = 'label', comType) {
        // 多选框
        if (comType === 'checkbox') {
          // 有可能数据，也有可能是逗号隔开的字符串
          let _value = value
          const str = []
          if (Object.prototype.toString.call(value) !== '[object Array]') {
            _value = value.split(',')
          }
          _value.forEach(v => {
            const item = list.find(item => item.value === v)
            if (item) {
              str.push(item[keyName])
            }
          })
          return str.join(',')
        } else {
          if (value) {
            return list.find(item => item.value === value)[keyName]
          }
        }

				return ''
			},
			showSelect(item) {
        if (this.ops.detail) return // 详情页不触发
				this.currentItem = item
				this.pickerConfig = {
					...this.pickerConfig,
					title: item.pickerTitle || '',
					keyName: item.keyName || 'label',
					dictData: [item.dictData],
				}
        this.$nextTick(() => {
          const ref = this.$refs[item.type]
          if (Object.prototype.toString.call(ref) === '[object Array]') {
            this.$refs[item.type][0].open()
          } else {
            this.$refs[item.type].open()
          }
        })
			},
			selectConfirm($event) {
				if (this.currentItem.type === 'select') {
					// todo 还要判断多选情况
					this.form[this.currentItem.prop] = $event.value[0].value
				}
        this.updateParentValue()
			},
			selectClose() {

			},
			radioChange(e, item) {
        this.updateParentValue()
			},
      checkboxChange(e, item) {
        console.log(e, item)
        this.currentItem = item
        this.form[item.prop] = e
        this.updateParentValue()
      },
			datetimeConfirm($event) {
        const item = this.currentItem
        const formatValue = $event.mode === 'time' ? this.formatTime(this.getTimestampFromTime($event.value), $event
            .mode) : this.formatTime($event.value, $event.mode)
        this.form[item.prop] = item.returnTimestamp ? ($event.mode === 'time' ? this
            .getTimestampFromTime($event.value) : $event.value) : formatValue; //是否返回时间搓
        this.updateParentValue()
			},
      calendarConfirm($event) {
        const item = this.currentItem
        const dateString = $event.reduce((b, n) => (b + `,${n}`))
        this.form[item.prop] = dateString
        this.updateParentValue()
      },
      keyboardConfirm(e) {
        console.log(e)
      },
      keyboardChange(value) {
        const item = this.currentItem
        this.form[item.prop] = `${this.form[item.prop]}${value}`
        this.updateParentValue()
      },
      keyboardBackspace() {
        // 删掉字符串最后一位
        const item = this.currentItem
        this.form[item.prop] = this.form[item.prop].slice(0, -1)
        this.updateParentValue()
      },
			/**
			 * validate 严格校验类型，需要指定type，比如type: 'number',
			 */
			submit() {
				return this.$refs.form.validate()
				// this.$refs.form.validate().then(res => {
				//   console.log(res)
				// }).catch(errors => {
				//   console.log(errors)
				// })
			},
			reset() {
				this.$refs.form.resetFields()
				this.$refs.form.clearValidate()
			},
      // 删除图片
      deletePic($event, item) {
        this.form[item.prop].splice($event.index, 1)
        this.updateParentValue()
      },
      // 新增图片
      afterRead($event, item) {
        // 当设置 mutiple 为 true 时, file 为数组格式，否则为对象格式
        const value = item.multiple ? $event.file : [$event.file]
        this.form[item.prop] = value
        this.updateParentValue()
      },
      updateParentValue() {
        // #ifdef VUE3
        this.$emit('update:modelValue', this.form)
        // #endif
        // #ifdef VUE2
        this.$emit("input", this.form)
        // #endif
      }
		}
	}
</script>

<template>
	<view>
		<uv-form
      ref="form"
      :model="form"
    >
      <template v-if="ops.detail">
        <template v-for="item in ops.columns">
          <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
            <view v-if="['select', 'radio', 'checkbox'].includes(item.type)">
              {{selectValue(form[item.prop], item.dictData, item.keyName, item.type)}}
            </view>
            <view style="white-space: pre-wrap" v-else>
              {{form[item.prop]}}
            </view>
          </uv-form-item>
        </template>
      </template>
      <template v-else>
        <template v-for="item in ops.columns">
          <template v-if="item.type === 'select'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item" @click="showSelect(item)">
              <uv-input
                  class="disabled"
                  disabled
                  :border="item.border || option.border"
                  :value="selectValue(form[item.prop], item.dictData, item.keyName)"
                  :placeholder="`${item.placeholder || '请选择' + item.label}`"
                  v-bind="item"
              >
              </uv-input>
              <template v-slot:right>
                <uv-icon name="arrow-down-fill"></uv-icon>
              </template>
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'radio'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
              <uv-radio-group v-model="form[item.prop]" @change="radioChange($event, item)">
                <uv-radio v-for="(radioItem, radioIndex) in item.dictData" :key="radioIndex" :label="radioItem.label"
                          :name="radioItem.value" :customStyle="{marginLeft: '8px', marginBottom: '8px'}" />
              </uv-radio-group>
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'datetime'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item" @click="showSelect(item)">
              <uv-input
                  class="disabled"
                  :value="form[item.prop]" disabled
                  :placeholder="`${item.placeholder || '请选择' + item.label}`"
                  :border="item.border || option.border"
                  v-bind="item"
              >
              </uv-input>
              <template v-slot:right>
                <uv-icon name="arrow-down-fill"></uv-icon>
              </template>
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'calendar'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item" @click="showSelect(item)">
              <uv-input
                  class="disabled"
                  disabled
                  :border="item.border || option.border"
                  :value="form[item.prop]"
                  :placeholder="`${item.placeholder || '请选择' + item.label}`"
                  v-bind="item"
              >
              </uv-input>
              <template v-slot:right>
                <uv-icon name="arrow-down-fill"></uv-icon>
              </template>
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'checkbox'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
              <uv-checkbox-group :value="form[item.prop]" @change="checkboxChange($event, item)">
                <uv-checkbox
                    :customStyle="{marginLeft: '8px', marginBottom: '8px'}"
                    v-for="(checkItem, checkIndex) in item.dictData"
                    :key="checkIndex"
                    :label="checkItem.label"
                    :name="checkItem.value"
                ></uv-checkbox>
              </uv-checkbox-group>
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'textarea'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
              <uv-textarea
                  v-model="form[item.prop]"
                  v-bind="item"
                  :border="item.border || option.border"
                  :placeholder="`${item.placeholder || '请选择' + item.label}`"
              />
            </uv-form-item>
          </template>
          <template v-else-if="item.type === 'number'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item" @click="showSelect(item)">
              <uv-input
                  class="disabled"
                  disabled
                  v-model="form[item.prop]"
                  :border="item.border || option.border"
                  :placeholder="`${item.placeholder || '请输入' + item.label}`"
                  v-bind="item"
              />
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'file'">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
              <uv-upload
                  :fileList="form[item.prop]" :disabled="item.disabled" :accept="item.accept"
                  :capture="item.capture" :maxCount="item.maxCount" :sizeType="item.sizeType"
                  :compressed="item.compressed" :camera="item.camera" :multiple="item.multiple" :maxSize="item.maxSize"
                  :previewImage="item.previewImage" width="150rpx" height="150rpx" @afterRead="afterRead($event, item)"
                  @delete="deletePic($event, item)"
              />
            </uv-form-item>
          </template>

          <template v-else-if="item.type === 'input' || !item.type">
            <uv-form-item :label="item.label" :prop="item.prop" :borderBottom="option.borderBottom" v-bind="item">
              <template v-if="item.isSlot">
                <slot :name="item.prop" />
              </template>
              <template v-else>
                <uv-input
                    :border="item.border || option.border"
                    v-model="form[item.prop]"
                    @input="updateParentValue"
                    v-bind="item"
                    :placeholder="`${item.placeholder || '请输入' + item.label}`"
                >
                  <template #prefix v-if="item.isPrefix">
                    <slot :name="item.prefixName"></slot>
                  </template>
                  <template #suffix v-if="item.isSuffix">
                    <slot :name="item.suffixName"></slot>
                  </template>
                </uv-input>
              </template>
            </uv-form-item>
          </template>
        </template>
      </template>

		</uv-form>

    <uv-keyboard ref="number" mode="number" :showCancel="false" :showConfirm="false" :dotDisabled="currentItem.dotDisabled" @confirm="keyboardConfirm" @change="keyboardChange" @backspace="keyboardBackspace" />
    <uv-calendar ref="calendar" v-bind="currentItem" @confirm="calendarConfirm" />
    <uv-datetime-picker v-if="currentItem.type === 'datetime'" ref="datetime" :value="form[currentItem.prop]" :mode="currentItem.mode" @confirm="datetimeConfirm" />
		<uv-picker safeAreaInsetBottom ref="select" :title="pickerConfig.title" :keyName="pickerConfig.keyName"
			:columns="pickerConfig.dictData" @confirm="selectConfirm($event)" @close="selectClose" />
	</view>
</template>

<style scoped lang="scss">
.disabled{
  background-color: transparent!important;
}
</style>
