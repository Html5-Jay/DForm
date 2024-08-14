<template>
	<view class="content">
	  <d-form ref="daForm" v-model="form" :option="option">
	    <template #cccs>
	      <uv-input style="width: 200px" v-model="form['cccs']" placeholder="cccsss" />
	    </template>
	    <template #srkSuffix>
	      <uv-code
	          ref="uCode"
	          seconds="20"
	          changeText="X秒重新获取哈哈哈"
	      ></uv-code>
	      <uv-button
	          text="tips"
	          type="success"
	          size="mini"
	      ></uv-button>
	    </template>
	    <template #srkPrefix>
	      <view>111123123</view>
	    </template>
	  </d-form>
	  <view class="btn-list">
	    <uv-button type="primary" @click="submit">提交表单</uv-button>
	    <uv-button @click="reset">重置表单</uv-button>
	  </view>
	</view>
</template>

<script>
	import DForm from '@/uni_modules/Jay-DForm/components/Jay-DForm/Jay-DForm.vue'
	export default {
		components: {
			DForm
		},
		data() {
			return {
				form: {
					xq: [1, 2],
					cccs: 123,
					srk: '123123',
					sex: 1,
					age: 2,
					textarea: '发来的激发山莨菪碱法律上电极法发来的激发山莨菪碱法律上电极法发来的激发山法律上\n111电极法发来的激发山莨菪碱法律上电极法发来的激发山莨菪碱法律上电极法发来的激发山莨菪碱法律上电极法'
				},
				//表单配置项
				option: {
				  labelWidth: '60',
				  borderBottom: true,
				  border: 'none',
					// detail: false, // 是否详情页
				  columns: [
				    {
				      placeholder: "请选择年龄段",
				      label: "年龄",
				      type: "radio",
				      prop: 'age',
				      dictData: [
				        {
				          value: 2,
				          label: "16~20岁",
				        },
				        {
				          value: 3,
				          label: "21~25岁",
				        },
				        {
				          value: 4,
				          label: "26~30岁",
				        },
				      ],
				      rules: [
				        {
				          type: 'number',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      label: "插槽测试",
				      prop: 'cccs',
				      isSlot: true
				    },
				    {
				      label: "输入框",
				      prop: 'srk',
				    },
				    {
				      label: "获客时间",
				      type: "datetime",
				      prop: 'time',
				      mode: 'datetime', //	date为日期选择，time为时间选择，year-month为年月选择 ,datetime日期时间选择
				      returnTimestamp: false, //value值是否返回时间搓格式
				      rules: [
				        {
				          // type: 'number',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      placeholder: "请选择兴趣",
				      label: "兴趣",
				      type: "checkbox",
				      prop: 'xq',
				      dictData: [{
				        value: 1,
				        label: "篮球",
				        disabled: true, //是否禁用
				      },
				        {
				          value: 2,
				          label: "足球",
				        },
				        {
				          value: 3,
				          label: "羽毛球",
				        },
				        {
				          value: 4,
				          label: "唱歌",
				        },
				        {
				          value: 5,
				          label: "街舞",
				        },
				      ],
				      rules: [
				        {
				          type: 'array',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      placeholder: "选择日期",
				      label: "日期",
				      prop: 'date',
				      type: "calendar",
				      mode: 'range', //	mode为single只能选择单个日期 为multiple可以选择多个日期为range可以选择日期范围
				      returnTimestamp: false, //value值是否返回时间搓格式
				      rules: [
				        {
				          // type: 'number',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      placeholder: "(例如：前端、后端)",
				      label: "经历",
				      prop: 'textarea',
				      type: "textarea",
				      count: true,
				      rules: [
				        {
				          // type: 'number',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      label: "数字键盘",
				      prop: 'number',
				      type: "number",
				      dotDisabled: true,
				      rules: [
				        {
				          type: 'string',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				
				    {
				      label: "性别",
				      // pickerTitle: '性别1',
				      type: "select",
				      prop: 'sex',
				      border: 'bottom',
				      dictData: [{
				        value: 1,
				        label: "男",
				      },
				        {
				          value: 2,
				          label: "女",
				        },
				      ],
				      rules: [
				        {
				          type: 'number',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				    {
				      label: "孩子头像 ",
				      type: "file",
				      prop: 'file',
				      maxCount: 1, //最大选择图片的数量
				      multiple: false, //是否开启图片多选，部分安卓机型不支持  false	true
				      accept: 'image', //接受的文件类型，file只支持H5（只有微信小程序才支持把accept配置为all、media）  可选 all | media | image | file | video
				      capture: ['album', 'camera'], // 图片或视频拾取模式，当accept为image类型时设置capture可选额外camera可以直接调起摄像头 String | Array
				      sizeType: ['original', 'compressed'], //original 原图，compressed 压缩图，默认二者都有，H5无效
				      compressed: true, //当accept为video时生效，是否压缩视频，默认为true  Boolean	true	false
				      camera: 'back', //当accept为video时生效，可选值为back或front  	String	back	-
				      maxSize: Number.MAX_VALUE, // 选择单个文件的最大大小，单位B(byte)，默认不限制	String | Number
				      previewImage: true, //是否在上传完成后展示预览图	Boolean	true	false
				      disabled: false, //是否禁用
				      rules: [
				        {
				          type: 'array',
				          required: true,
				          message: '此为必填字段',
				          trigger: ['blur', 'change']
				        }
				      ],
				    },
				  ]
				},
			}
		},
		onLoad() {

		},
		methods: {
		  // 提交表单
		  submit() {
		    // this.$refs.daForm.submit()
		    this.$refs.daForm.submit().then(res => {
		      console.log('success')
		    }).catch(error => {
		      console.log(error)
		    })
		  },
		  //重置表单
		  reset() {
		    this.$refs.daForm.reset()
		  }
		},
	}
</script>

<style>
	.content{
	  padding: 32rpx;
	}
</style>
