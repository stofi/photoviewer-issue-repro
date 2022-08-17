<template>
  <div id="photoviewer-container"></div>
</template>

<script lang="ts">
import { computed, defineComponent, onMounted, PropType, ref } from 'vue'

import { closeOutline } from 'ionicons/icons'

import {
  capShowOptions,
  capShowResult,
  Image,
  PhotoViewer,
  ViewerOptions,
} from '@capacitor-community/photoviewer'

export default defineComponent({
  name: 'AppPhotoViewer',
  props: {
    images: {
      type: Array as PropType<Image[]>,
      required: true,
    },
    value: {
      type: Boolean as PropType<boolean>,
      required: true,
    },
  },

  setup(props,context) {


    const imageList = computed(() => {
      return props.images
    })

    const mode = 'one'
    const startFrom = 0

    const options: ViewerOptions = {} as ViewerOptions

    const show = async (
      imageList: Image[],
      mode: string,
      startFrom: number,
      options?: ViewerOptions
    ): Promise<capShowResult> => {
      const opt: capShowOptions = {} as capShowOptions
      opt.images = imageList
      opt.mode = mode
      opt.startFrom = startFrom
      if (options) opt.options = options

      try {
        const ret = await PhotoViewer.show(opt)
        context.emit('input', false)
        
        if (ret.result) {
          return Promise.resolve(ret)
        } else {
          return Promise.reject(ret)
        }
      } catch (err: any) {
        const ret: capShowResult = {} as capShowResult
        ret.result = false
        ret.message = err.message

        return Promise.reject(ret)
      }
    }

    const showToast = async (message: string) => {
      alert(message)
    }

    onMounted(async () => {
      let ret: any

      try {
        ret = await show(imageList.value, mode, startFrom, options)

        if (!ret.result) {
          await showToast(JSON.stringify(ret))
        }
      } catch (err: any) {
        await showToast(err.message)
      }
    })

    return {
      options,
      closeOutline,
    }
  },
})
</script>

