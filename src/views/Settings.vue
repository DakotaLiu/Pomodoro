<template>
  <div id="settings">
    <div class="b-table">
    <b-table variant="warning" :items="items" :fields="fields" @row-clicked="selectAlarm">
      <template v-slot:cell(preview)="data">
        <audio controls :src="'./alarms/'+data.item.file"></audio>
      </template>
      <template v-slot:cell(select)="data">
        <font-awesome-icon v-if="data.item.file ==alarm" :icon="['fas','check']"></font-awesome-icon>
      </template>
    </b-table>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      items: [
        {
          name: 'swing',
          file: 'swing.wav'
        },
        {
          name: 'cello',
          file: 'cello.wav'
        },
        {
          name: 'piano',
          file: 'piano.wav'
        },
        {
          name: 'drum',
          file: 'drum.wav'
        },
        {
          name: 'softcello',
          file: 'softcello.wav'
        }
      ],
      fields: [
        {
          key: 'name',
          label: '名稱'
        },
        {
          key: 'preview',
          label: '預覽'
        },
        {
          key: 'select',
          label: '選擇'
        }
      ]
    }
  },
  computed: {
    alarm () {
      return this.$store.getters.alarm
    }
  },
  methods: {
    selectAlarm (item) {
      this.$store.commit('selectAlarm', item.file)
    }
  }
}
</script>

<style lang="stylus">
.b-table
  color white
</style>
