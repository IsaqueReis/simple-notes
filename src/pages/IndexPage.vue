<template>
  <q-page class="container">
    <div class="row justify-end">
      <div class="col-12">
        <q-input
          outlined
          v-model="task"
          label="Task"
          placeholder="enter your task name here"
          dense
        >
          <template v-slot:append>
            <q-icon
              v-if="task !== ''"
              name="close"
              @click="task = ''"
              class="cursor-pointer"
            />
          </template>
          <template v-slot:after>
            <q-input
              outlined
              v-model="countdown"
              type="number"
              label="Time (in minutes)"
              v-if="mode"
            ></q-input>
            <q-btn round dense flat icon="add" @click="addTask"></q-btn>
          </template>
        </q-input>
      </div>
    </div>
    <div class="row q-mt-md">
      <div class="col-12">
        <q-input
          outlined
          v-model="description"
          type="textarea"
          label="Task"
          placeholder="enter your task description here"
          dense
        />
      </div>
    </div>
    <div class="row q-mt-md">
      <div class="col-12">
        <q-toggle v-model="mode" :label="mode ? 'Countdown' : 'Timer'" />
      </div>
    </div>
    <div class="row q-mt-xl">
      <q-item-label header v-if="tasks.length > 0">Tasks</q-item-label>
      <div class="col-12" v-for="task in tasks" :key="task.value">
        <q-item tag="label" v-ripple>
          <q-item-section side top>
            <q-checkbox
              v-model="task.value"
              @click="checkTask(task)"
            ></q-checkbox>
          </q-item-section>
          <q-item-section>
            <q-item-label
              ><div :class="task.value ? 'text-strike text-bold' : 'text-bold'">
                {{ task.name }} -
                <strong
                  :style="
                    task.timer.mode === 'Countdown'
                      ? 'color: red'
                      : 'color:blue'
                  "
                  >{{ task.timer.label }}</strong
                >
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
                flat
                round
                class="gt-xs"
                icon="restart_alt"
                @click="restartTask(task)"
              ></q-btn>
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
        <q-separator inset="item" />
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from 'vue';
import { useQuasar } from 'quasar';

export default defineComponent({
  name: 'IndexPage',
  setup() {
    const $q = useQuasar();
    return {
      showLoading() {
        $q.loading.show();
      },
      hideLoading() {
        $q.loading.hide();
      },
      notify(type, position, message) {
        $q.notify({
          timeout: 300000,
          type: type,
          position: position,
          message: message,
          actions: [{ label: 'Dismiss', color: 'yellow', handler: () => {} }],
        });
      },
    };
  },
  data() {
    return {
      task: '',
      description: '',
      tasks: [],
      countdown: 1,
      mode: ref(false),
    };
  },
  methods: {
    restartTask(task) {
      if (task.timer.timeoutFn) clearTimeout(task.timer.timeoutFn);

      task.timer.countdown = task.timer.reference.countdown;
      task.timer.label = task.timer.reference.label;
      task.value = false;
    },
    startTask(task) {
      if (task.value) return;
      if (task.timer.mode === 'Countdown' && task.timer.countdown <= 0) {
        this.notify(
          'negative',
          'center',
          `time is over (goal: ${task.timer.reference.label})`
        );
        task.timer.label = `time is over (goal: ${task.timer.reference.label})`;
        return;
      }

      task.timer.timeoutFn = setTimeout(() => {
        if (task.timer.mode === 'Countdown') task.timer.countdown -= 1;
        else task.timer.countdown += 1;
        let minutes = parseInt(task.timer.countdown / 60, 10);
        let seconds = parseInt(task.timer.countdown % 60, 10);
        task.timer.label = `${minutes < 10 ? '0' + minutes : minutes}:${
          seconds < 10 ? '0' + seconds : seconds
        }`;
        this.startTask(task);
      }, 1000);
    },
    pauseTask(task) {
      if (task.timer.timeoutFn) clearTimeout(task.timer.timeoutFn);
    },
    addTask() {
      if (!this.description || !this.task) {
        this.notify(
          'negative',
          'center',
          `Please enter the task and its description!`
        );
        return;
      }
      let mode = this.mode ? 'Countdown' : 'Timer';
      let countdownInSeconds = this.countdown * 60;
      let minutes = parseInt(countdownInSeconds / 60, 10);
      let seconds = parseInt(countdownInSeconds % 60, 10);
      let label =
        mode === 'Countdown'
          ? `${minutes < 10 ? '0' + minutes : minutes}:${
              seconds < 10 ? '0' + seconds : seconds
            }`
          : '00:00';
      this.tasks.push({
        name: this.task,
        description: this.description,
        value: false,
        timer: {
          reference: {
            countdown: mode === 'Countdown' ? countdownInSeconds : 0,
            label: label,
          },
          mode: mode,
          countdown: mode === 'Countdown' ? countdownInSeconds : 0,
          label: label,
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
    checkTask(task) {
      if (task.value) this.pauseTask(task);
    },
  },
});
</script>
