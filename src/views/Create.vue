<template>
  <div class="row">
    <div class="col s6 offset-s3">
      <h1>Create</h1>
      <form @submit.prevent="submitted">
        <div class="input-field ">
          <input id="title" v-model="title " type="text" class="validate" required>
          <label for="title">Title</label>
          <span class="helper-text" data-error="Title is required"></span>
        </div>
        <div class="chips" ref="chips"></div>
        <div class="input-field">
          <textarea id="description" class="materialize-textarea" v-model="description" maxlength="2048"></textarea>
          <label for="description">Description</label>
          <span class="character-counter" style="float: right; font-size: 12px;">{{description.length}}/2048</span>
        </div>
        <input type="text" ref="datepicker">
        <button class="btn" type="submit">Submit</button>
      </form>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Create',
  data: () => ({
    description: '',
    title: '',
    chips: null,
    date: null
  }),
  computed: {
  },
  mounted() {
    this.chips = M.Chips.init(this.$refs.chips, {
      placeholder: 'Task tags'
    })
    this.date = M.Datepicker.init(this.$refs.datepicker,{
      format: 'dd.mm.yyyy',
      defaultDate: new Date,
      setDefaultDate: true
    });
  },
  methods: {
    submitted() {
      const task = {
        title: this.title,
        description: this.description,
        id: Date.now(),
        tags: this.chips.chipsData,
        date: this.date.date,
        status: new Date(this.date) > new Date() ? 'active' : 'outdated'
      }
      this.$store.dispatch('createTask', task)
      this.$router.push('list')
    }
  },
  destroyed() {
    if (this.date && this.date.destroy) {
      this.date.destroy();
    }
    if (this.chips && this.chips.destroy) {
      this.chips.destroy();
    }
  }
}
</script>
