<template>
  <div></div>
</template>

<script lang="ts">

// import '../../../QbForm-js/dist/QbForm-default.css'
// import '../../QbForm-js/dist/QbForm-compact.css'
// import '../../QbForm-js/dist/QbForm-dark.css'

import { Component, Prop, Vue } from 'vue-property-decorator'

interface QbFormJs {
  shadowRoot: ShadowRoot | null;
  /* eslint-disable @typescript-eslint/no-explicit-any */
  display(htmlElement: HTMLElement | null): void;
  setProperty (element: string | string[], property: string, value: any): void;
  getProperty (element: string | string[], property: string): void;
  setCallback (elementPath: string | string[], cbName: string, cb: (inputParam: object | null) => boolean): void;
  /* eslint-enable @typescript-eslint/no-explicit-any */
}

/* eslint-disable-next-line @typescript-eslint/no-explicit-any */
interface WindowEx { [index: string]: any }

@Component
export default class QbForm extends Vue {
  @Prop() schema!: object
  @Prop({ default: 'default' }) theme!: string

  private qbFormJs: QbFormJs | null = null

  public constructor () {
    super()
    const windowEx = window as unknown as WindowEx
    this.qbFormJs = new windowEx.QbForm(this.schema, this.theme) as QbFormJs
  }

  mounted () {
    if (this.qbFormJs) {
      const domElt = this.$vnode.elm as HTMLElement
      const shadow: ShadowRoot = domElt.attachShadow({ mode: 'open' })
      this.qbFormJs.shadowRoot = shadow
      const newDiv = document.createElement('div')
      shadow.appendChild(newDiv)
      // Duplicate StyleSheets starting with 'QbForm' from document -> shadow
      const sourceList = document.styleSheets
      for (let i = 0; i < sourceList.length; i++) {
        const source = sourceList[i]
        const href = source.href
        if (href) {
          const fields = href.split('/')
          if (fields) {
            const basename = fields[fields.length - 1].toUpperCase()
            if (basename.startsWith('QBFORM-') && basename.endsWith('.CSS')) {
              const link = document.createElement('link')
              link.type = 'text/css'
              link.rel = 'stylesheet'
              link.href = href
              shadow.appendChild(link)
            }
          }
        }
      }
      this.qbFormJs.display(newDiv)
    }
  }

  /* eslint-disable-next-line @typescript-eslint/no-explicit-any */
  public getProperty (elementPath: string | string[], param: string): any {
    let value = null
    if (this.qbFormJs) {
      value = this.qbFormJs.getProperty(elementPath, param)
    }
    return value
  }

  /* eslint-disable-next-line @typescript-eslint/no-explicit-any */
  public setProperty (elementPath: string | string[], param: string, value: any) {
    if (this.qbFormJs) {
      value = this.qbFormJs.setProperty(elementPath, param, value)
    }
    return value
  }

  /* eslint-disable-next-line @typescript-eslint/no-explicit-any */
  public setCallback (elementPath: string | string[], cbName: string, cb: (inputParam: object | null) => boolean): void {
    if (this.qbFormJs) {
      this.qbFormJs.setCallback(elementPath, cbName, cb)
    }
  }
}
</script>
