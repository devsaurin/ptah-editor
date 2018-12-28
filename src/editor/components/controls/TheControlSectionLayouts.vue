<script>
import { mapState, mapActions } from 'vuex'
import * as _ from 'lodash-es'

export default {
  name: 'ControlSectionLayouts',
  props: {
    builder: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      controlOpen: true,
      group: '',
      layouts: [],
      selectedSection: ''
    }
  },

  computed: {
    ...mapState('Sidebar', [
      'builderSections',
      'builderGroups',
      'settingObjectSection'
    ])
  },

  created () {
    let name = this.settingObjectSection.name
    this.selectedSection = name
    this.group = _.find(this.builderSections, { name }).group
    this.layouts = this.builderGroups[this.group]
  },

  methods: {
    ...mapActions('Sidebar', [
      'clearSettingObject'
    ]),

    selectLayout (section) {
      if (section.name === this.selectedSection) {
        return false
      }

      let index = _.findIndex(this.builder.sections, ['name', this.selectedSection])
      this.builder.remove(this.settingObjectSection)
      this.clearSettingObject()
      this.builder.add(section, index)

      setTimeout(() => {
        document
          .getElementById('sections_contents')
          .querySelectorAll('.b-menu-subitem')[index]
          .dispatchEvent(new Event('click'))
      }, 250)
    }
  }
}
</script>

<template>
  <div class="b-bg-controls" v-if="this.layouts.length">
    <div class="b-bg-controls__header" @click="controlOpen = !controlOpen">
      <span>Layout</span> <i :class="{ 'dropped': !controlOpen }"><icon-base name="arrowDropDown" width="8"></icon-base></i>
    </div>
    <base-dropdown :isOpened="controlOpen" :hasOverflow="controlOpen">
      <div v-for="(section, index) in layouts"  class="b-add"
           :class="{ 'b-add-selected': section.name === selectedSection }"
           :key="index"
           @click="selectLayout(section)">
        <img class="b-add-image" v-if="section.cover" :src="section.cover"/>
        <icon-base
          v-if="section.name === selectedSection"
          name="checkMark"
          :width="17"
          :height="12"
          color="#fff">
        </icon-base>
        <span class="b-add-title" v-if="!section.cover">
          {{ section.name }}
        </span>
      </div>
    </base-dropdown>
  </div>
</template>

<style lang="sass" scoped>
.b-bg-controls
  &__header
    font-size: 1.6rem
    height: 3.2rem
    color: #272727
    display: flex
    align-items: center
    cursor: pointer
    i
      margin-left: 5px
      margin-bottom: -5px
      transform: rotate(180deg)
      &.dropped
        transform: rotate(0deg)
  &__control
    margin-top: 2.2rem
.b-add
  overflow: hidden
  width: 17.6rem
  height: 11.2rem
  box-sizing: border-box
  margin: 0 auto 1.2rem
  cursor: pointer
  display: flex
  align-items: center
  justify-content: center
  border: 0.2rem solid transparent
  transition: all 0.1s ease-in-out
  position: relative
  &-image
    max-width: 100%
    max-height: 100%
  &-selected:after
    content: ''
    display: block
    position: absolute
    width: 100%
    height: 100%
    background: rgba(67, 11, 238, .3)
    z-index: 100
  svg
    position: absolute
    top: calc(50% - 6px)
    left: calc(50% - 8.5px)
    z-index: 110
</style>