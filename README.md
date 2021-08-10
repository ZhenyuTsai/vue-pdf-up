# vue-pdf-up
vue.js pdf viewer is a package for Vue that enables you to display and view PDF's easily via vue components.

## Install 
```bash
npm install vue-pdf-up
```

## Example - basic
```vue
<template>
  <vue-pdf-up src="https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf"></vue-pdf-up>
</template>

<script>
import vuePdfUp from 'vue-pdf-up'

export default {
  components: {
    vuePdfUp
  }
}
```

## Browser support
Same browser support as [Vue.js 2](https://github.com/vuejs/vue/blob/dev/README.md)

## API

### Props

#### :src <sup>String / Object - default: ''<sup>
The url of the given pdf. `src` may also be a `string|TypedArray|DocumentInitParameters|PDFDataRangeTransport` for more details, see [`PDFJS.getDocument()`](https://github.com/mozilla/pdf.js/blob/8ff1fbe7f819513e7d0023df961e3d223b35aefa/src/display/api.js#L117).

#### :page <sup>Number - default: 1<sup>
The page number to display.

#### :rotate <sup>Number - default: 0<sup>
The page rotation in degrees, only multiple of 90 are valid.
EG: 90, 180, 270, 360, ...


## Examples

##### Example - current page / page count
```vue
<template>
	<div>
		{{currentPage}} / {{pageCount}}
		<vue-pdf-up
			src="https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf"
			@num-pages="pageCount = $event"
			@page-loaded="currentPage = $event"
		></vue-pdf-up>
	</div>
</template>

<script>

import vuePdfUp from 'vue-pdf-up'

export default {
	components: {
		vuePdfUp
	},
	data() {
		return {
			currentPage: 0,
			pageCount: 0,
		}
	}
}

</script>
```


##### Example - display multiple pages of the same pdf document
```vue
<template>
	<div>
		<vue-pdf-up
			v-for="i in numPages"
			:key="i"
			:src="src"
			:page="i"
			style="display: inline-block; width: 25%"
		></vue-pdf-up>
	</div>
</template>

<script>

import vuePdfUp from 'vue-pdf-up'

var loadingTask = vuePdfUp.createLoadingTask('https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf');

export default {
	components: {
		vuePdfUp
	},
	data() {
		return {
			src: loadingTask,
			numPages: undefined,
		}
	},
	mounted() {

		this.src.promise.then(pdf => {

			this.numPages = pdf.numPages;
		});
	}
}

</script>
```


##### Example - print all pages
```vue
<template>
	<button @click="$refs.myPdfComponent.print()">print</button>
	<vue-pdf-up ref="myPdfComponent" src="https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf"></vue-pdf-up>
</template>
```


##### Example - print multiple pages
```vue
<template>
	<button @click="$refs.myPdfComponent.print(100, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14])">print</button>
	<vue-pdf-up ref="myPdfComponent" src="https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf"></vue-pdf-up>
</template>
```


##### Example - get text content
```vue
<template>
	<div>
		<button
			@click="logContent"
		>
			log content
		</button>
		<vue-pdf-up
			ref="myPdfComponent"
			src="https://hd2-test-out.oss-cn-shanghai-finance-1-pub.aliyuncs.com/test/camp/tsr/file/2021/06/03/P3100C02007000200262971_1622442901546.pdf"
		></vue-pdf-up>
	</div>
</template>

<script>

import vuePdfUp from 'vue-pdf-up'

export default {
	components: {
		vuePdfUp
	},
	methods: {
		logContent() {

			this.$refs.myPdfComponent.pdf.forEachPage(function(page) {

				return page.getTextContent()
				.then(function(content) {

					var text = content.items.map(item => item.str);
					console.log(text);
				})
			});
		}
	}
}

</script>
```


##### Example - complete
```vue
<template>
	<div>
		<input type="checkbox" v-model="show">
		<select v-model="src" style="width: 30em">
			<option v-for="item in pdfList" :value="item" v-text="item"></option>
		</select>
		<input v-model.number="page" type="number" style="width: 5em"> /{{numPages}}
		<button @click="rotate += 90">&#x27F3;</button>
		<button @click="rotate -= 90">&#x27F2;</button>
		<button @click="$refs.pdf.print()">print</button>
		<div style="width: 50%">
			<div v-if="loadedRatio > 0 && loadedRatio < 1" style="background-color: green; color: white; text-align: center" :style="{ width: loadedRatio * 100 + '%' }">{{ Math.floor(loadedRatio * 100) }}%</div>
			<vue-pdf-up v-if="show" ref="pdf" style="border: 1px solid red" :src="src" :page="page" :rotate="rotate" @password="password" @progress="loadedRatio = $event" @error="error" @num-pages="numPages = $event" @link-clicked="page = $event"></vue-pdf-up>
		</div>
	</div>
</template>
<script>
import vuePdfUp from 'vue-pdf-up'

export default {
	components: {
		'vue-pdf-up': vuePdfUp
	},
	data () {
		return {
			show: true,
			pdfList: [
				'',
				'https://cdn.mozilla.net/pdfjs/tracemonkey.pdf',
				'https://cdn.rawgit.com/mozilla/pdf.js/c6e8ca86/test/pdfs/freeculture.pdf',
				'https://cdn.rawgit.com/mozilla/pdf.js/c6e8ca86/test/pdfs/annotation-link-text-popup.pdf',
				'https://cdn.rawgit.com/mozilla/pdf.js/c6e8ca86/test/pdfs/calrgb.pdf',
				'https://cdn.rawgit.com/sayanee/angularjs-pdf/68066e85/example/pdf/relativity.protected.pdf',
				'data:application/pdf;base64,JVBERi0xLjUKJbXtrvsKMyAwIG9iago8PCAvTGVuZ3RoIDQgMCBSCiAgIC9GaWx0ZXIgL0ZsYXRlRGVjb2RlCj4+CnN0cmVhbQp4nE2NuwoCQQxF+/mK+wMbk5lkHl+wIFislmIhPhYEi10Lf9/MVgZCAufmZAkMppJ6+ZLUuFWsM3ZXxvzpFNaMYjEriqpCtbZSBOsDzw0zjqPHZYtTrEmz4eto7/0K54t7GfegOGCBbBdDH3+y2zsMsVERc9SoRkXORqKGJupS6/9OmMIUfgypJL4KZW5kc3RyZWFtCmVuZG9iago0IDAgb2JqCiAgIDEzOAplbmRvYmoKMiAwIG9iago8PAogICAvRXh0R1N0YXRlIDw8CiAgICAgIC9hMCA8PCAvQ0EgMC42MTE5ODcgL2NhIDAuNjExOTg3ID4+CiAgICAgIC9hMSA8PCAvQ0EgMSAvY2EgMSA+PgogICA+Pgo+PgplbmRvYmoKNSAwIG9iago8PCAvVHlwZSAvUGFnZQogICAvUGFyZW50IDEgMCBSCiAgIC9NZWRpYUJveCBbIDAgMCA1OTUuMjc1NTc0IDg0MS44ODk3NzEgXQogICAvQ29udGVudHMgMyAwIFIKICAgL0dyb3VwIDw8CiAgICAgIC9UeXBlIC9Hcm91cAogICAgICAvUyAvVHJhbnNwYXJlbmN5CiAgICAgIC9DUyAvRGV2aWNlUkdCCiAgID4+CiAgIC9SZXNvdXJjZXMgMiAwIFIKPj4KZW5kb2JqCjEgMCBvYmoKPDwgL1R5cGUgL1BhZ2VzCiAgIC9LaWRzIFsgNSAwIFIgXQogICAvQ291bnQgMQo+PgplbmRvYmoKNiAwIG9iago8PCAvQ3JlYXRvciAoY2Fpcm8gMS4xMS4yIChodHRwOi8vY2Fpcm9ncmFwaGljcy5vcmcpKQogICAvUHJvZHVjZXIgKGNhaXJvIDEuMTEuMiAoaHR0cDovL2NhaXJvZ3JhcGhpY3Mub3JnKSkKPj4KZW5kb2JqCjcgMCBvYmoKPDwgL1R5cGUgL0NhdGFsb2cKICAgL1BhZ2VzIDEgMCBSCj4+CmVuZG9iagp4cmVmCjAgOAowMDAwMDAwMDAwIDY1NTM1IGYgCjAwMDAwMDA1ODAgMDAwMDAgbiAKMDAwMDAwMDI1MiAwMDAwMCBuIAowMDAwMDAwMDE1IDAwMDAwIG4gCjAwMDAwMDAyMzAgMDAwMDAgbiAKMDAwMDAwMDM2NiAwMDAwMCBuIAowMDAwMDAwNjQ1IDAwMDAwIG4gCjAwMDAwMDA3NzIgMDAwMDAgbiAKdHJhaWxlcgo8PCAvU2l6ZSA4CiAgIC9Sb290IDcgMCBSCiAgIC9JbmZvIDYgMCBSCj4+CnN0YXJ0eHJlZgo4MjQKJSVFT0YK',
			],
			src:'',
			loadedRatio: 0,
			page: 1,
			numPages: 0,
			rotate: 0,
		}
	},
	methods: {
		password: function(updatePassword, reason) {
			updatePassword(prompt('password is "test"'));
		},
		error: function(err) {

			console.log(err);
		}
	}
}
</script>
```
