<template lang="pug">
  div
    #artboard.artboard(ref="artboard")
      component(v-for='section in $builder.sections'
        :is='section.name'
        :key='section.id'
        :id='section.id'
      )

    .controller
      .controller-intro(v-if="showIntro && !this.$builder.sections.length")
        h1 Hello, start your project
        .grid.is-center
          .column.is-screen-6
            .input.is-rounded
              input(placeholder="project name" v-model="title")

      ul.controller-list(:class="{ 'is-hidden': !listShown }")
        li(v-for="section in sections")
          a.controller-element(@click="addElement(section)")
            |{{ section }}

      .container
        .controller-buttons.grid.is-center
          .column.is-screen-3
            button.controller-add.button.is-blue.is-block(@click="addSection()" ref="addButton" :disabled="showIntro && !title.length")
              svg.icon(viewBox='0 0 24 24' class="is-large")
                path(d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z")
          .column.is-screen-3
            button.controller-submit.button.is-green.is-block(@click="submit")
              svg.icon(viewBox='0 0 24 24' class="is-large")
                path(d="M9 16.2L4.8 12l-1.4 1.4L9 19 21 7l-1.4-1.4L9 16.2z")
</template>

<script>
export default {
  name: 'b-builder',
  props: {
    showIntro: {
      type: Boolean,
      default: true
    },
    data: {
      type: Object,
      default: () => ({
        title: '',
        sections: []
      })
    }
  },
  data () {
    return {
      title: null,
      listShown: false,
      sections: Object.keys(this.$builder.components)
    }
  },
  watch: {
    title (value) {
      this.$builder.title = value;
    }
  },
  methods: {
    addSection () {
      // add the section immediatly if none are present.
      if (this.sections.length === 1) {
        this.addElement(this.sections[0]);
        return;
      }
      this.listShown = true;
    },
    addElement (name) {
      this.$builder.create({
        name: name,
        schema: this.$builder.components[name].options.$schema
      });
      this.listShown = false;
    },
    toogleEditableState () {
      this.$builder.isEditing = !this.$builder.isEditing;
    },
    submit () {
      this.$emit('saved', this.$builder);
    }
  },
  created () {
    // sets the initial data.
    this.$builder.set(this.data);
    this.title = this.$builder.title;
  },
  mounted () {
    this.$builder.rootEl = this.$refs.artboard;
  }
};
</script>

<style lang="stylus">
/**
 * colorful
 */
$blue   := #4da1ff
$green  := #38E4B7
$red    := #EA4F52

/**
 * grayscale
 */
$white  := #FFFFFF
$gray   := #CEDEE8
$dark   := #323c47
$black  := #000000

/**
 * color variation
 */
$shadow     := alpha($black, 5%)
$darkBlue   := darken($blue, 30%)
$darkGreen  := darken($green, 30%)
$darkRed    := darken($red, 30%)
$darkWhite  := darken($white, 30%)
$lightDark  := lighten($dark, 40%)
$transDark  := alpha($dark, 90%)
$transBlack := alpha($black, 90%)
$transWhite := alpha($white, 70%)
$transGray  := alpha($gray, 70%)

$floatHover
  cursor: pointer
  box-shadow: 0 14px 28px alpha($dark, 0.125), 0 10px 10px alpha($dark, 0.11)
  transform: translate(0, -1px)
  z-index: 2
.controller
  &-buttons
    padding: 50px
  &-submit
  &-add
    transition: 0.5s
    &:hover
      @extends $floatHover
  &-intro
    text-align: center
    font-size: 30px
    color: $dark
    font-weight: normal
  &-list
    background: $gray
    margin: 0
    padding: 20px
    display: flex
    justify-content: center
    align-items: center
    list-style: none
  &-element
    display: flex
    justify-content: center
    align-items: center
    width: 150px
    height: 150px
    margin: 5px
    border-radius: 10px
    background: darken($gray, 10%)
    transition: 0.5s
    cursor: pointer
    color: $white
    &:hover
      @extends $floatHover

.is-hidden
  display: none
</style>
