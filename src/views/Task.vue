<template>
  <div class="row">
    <div v-if="task" class="col s6 offset-s3">
      <h1>{{ task.title }}</h1>
      <form @submit.prevent="submitted">
        <div class="input-field">
          <textarea id="description"
                    :style="`height: ${heightTextArea}px`"
                    class="materialize-textarea"
                    ref="textarea"
                    v-model="description"
                    maxlength="2048"></textarea>
          <label for="description" class="active">Description</label>
          <span class="character-counter" style="float: right; font-size: 12px;">{{description.length}}/2048</span>
        </div>
        <div class="chips" ref="chips"></div>
        <input type="text" ref="datepicker">
        <div v-if="task.status !== 'completed'">
          <button class="btn" type="submit">Update</button>
          <button class="btn blue" style="margin-left: 1rem" type="button" @click="completeTask">Complete Task</button>
        </div>
      </form>
    </div>
    <h3 v-else>Task not found</h3>
  </div>
</template>

<script>
export default {
  name: 'Task',
  computed: {
    task() {
      return this.$store.getters.taskById(parseInt(this.$route.params.id, 10))
    }
  },
  data: () => ({
    description: '',
    chips: null,
    date: null,
    heightTextArea: 150
  }),
  mounted() {
    this.description = this.task.description
    setTimeout(() => {M.textareaAutoResize(this.$refs.textarea)}, 0)
    this.chips = M.Chips.init(this.$refs.chips, {
      placeholder: 'Task tags',
      data: this.task.tags
    })
    this.date = M.Datepicker.init(this.$refs.datepicker,{
      format: 'dd.mm.yyyy',
      defaultDate: new Date(this.task.date),
      setDefaultDate: true
    });
  },
  methods: {
    submitted() {
      this.$store.dispatch('updateTask', {
        id: this.task.id,
        description: this.description,
        date: this.date.date
      })
      this.$router.push('/list')
    },
    completeTask() {
      this.$store.dispatch('completeTask', this.task.id)
      this.$router.push('/list')
    },
    destroyed() {
      if (this.date && this.date.destroy) {
        this.date.destroy();
      }
      if (this.chips && this.chips.destroy) {
        this.chips.destroy();
      }
    }
  },
}
</script>

<style>
  .taskDescription {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #9e9e9e;
    border-radius: 0;
    outline: none;
    width: 100%;
    font-size: 16px;
    margin: 0 0 8px 0;
    padding: 0;
    box-shadow: none;
    box-sizing: content-box;
    transition: box-shadow .3s, border .3s;
    line-height: normal;
    padding: .8rem 0 .8rem 0;
    box-sizing: border-box;
    color: #000;
  }
</style>
