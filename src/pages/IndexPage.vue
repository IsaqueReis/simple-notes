<template>
  <q-page class="container">
    <div class="row justify-end">
      <div class="col-12">
        <q-input
          outlined
          v-model="description"
          label="Task"
          maxlength="500"
          placeholder="enter your task description here"
          dense
        >
          <template v-slot:append>
            <q-icon
              v-if="description !== ''"
              name="close"
              @click="description = ''"
              class="cursor-pointer"
            />
          </template>
          <template v-slot:hint>Caracteres</template>
          <template v-slot:after>
            <q-btn round dense flat icon="add" @click="addTask"></q-btn>
          </template>
        </q-input>
      </div>
    </div>
    <div class="row q-mt-xl">
      <q-item-label header v-if="tasks.length > 0">Tasks</q-item-label>
      <div class="col-12" v-for="task in tasks" :key="task.value">
        <q-item tag="label" v-ripple>
          <q-item-section side top>
            <q-checkbox v-model="task.value"></q-checkbox>
          </q-item-section>
          <q-item-section>
            <q-item-label
              ><div :class="task.value ? 'text-strike' : ''">
                {{ task.name }} -
                <strong style="color: red">{{ task.timer.label }}</strong>
              </div>
            </q-item-label>
            <q-item-label caption>
              <div :class="task.value ? 'text-strike' : ''">
                {{ task.description }}
              </div>
            </q-item-label>
          </q-item-section>
          <q-item-section top side>
            <div class="q-gutter-xs">
              <q-btn
                color="red"
                flat
                round
                class="gt-xs"
                icon="play_arrow"
                @click="startTask(task)"
              ></q-btn>
              <q-btn
                flat
                round
                class="gt-xs"
                icon="pause"
                @click="pauseTask(task)"
              ></q-btn>
              <q-btn
                flat
                round
                class="gt-xs"
                icon="delete"
                @click="deleteTask(task)"
              ></q-btn>
            </div>
          </q-item-section>
        </q-item>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'IndexPage',
  data() {
    return {
      description: '',
      tasks: [],
      countdown: 300,
    };
  },
  methods: {
    startTask(task) {
      if (task.timer.countdown > 0) {
        task.timer.timeoutFn = setTimeout(() => {
          task.timer.countdown -= 1;
          let minutes = parseInt(task.timer.countdown / 60, 10);
          let seconds = parseInt(task.timer.countdown % 60, 10);
          task.timer.label = `${minutes < 10 ? '0' + minutes : minutes}:${
            seconds < 10 ? '0' + seconds : seconds
          }`;
          this.startTask(task);
        }, 1000);
      }
    },
    pauseTask(task) {
      clearTimeout(task.timer.timeoutFn);
    },
    addTask() {
      if (!this.description) {
        return;
      }
      let minutes = parseInt(this.countdown / 60, 10);
      let seconds = parseInt(this.countdown, 10);
      this.tasks.push({
        name: `Task ${this.tasks.length + 1}`,
        description: this.description,
        value: false,
        timer: {
          countdown: this.countdown,
          label: `${minutes < 10 ? '0' + minutes : minutes}:${
            seconds < 10 ? '0' + seconds : seconds
          }`,
        },
        timeoutFn: null,
      });
    },
    deleteTask(task) {
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.tasks[i].name == task.name) {
          this.tasks.splice(i, 1);
          break;
        }
      }
    },
  },
});
</script>
