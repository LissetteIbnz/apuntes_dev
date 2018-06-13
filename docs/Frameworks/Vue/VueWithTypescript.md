# VueJS con Typescript

https://github.com/Microsoft/TypeScript-Vue-Starter#typescript-vue-starter

```ts
<template>
  <v-dialog
    ref="confirmActionPopup"
    v-model="active"
    max-width="290">
    <v-card>
      <v-card-title class="headline">{{ question }}</v-card-title>
      <v-card-text>
        <p class="title primary--text">{{ text }}</p>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-btn
          color="error darken-1"
          flat
          @click.native="confirmAction">Yes</v-btn>
        <v-btn
          color="primary darken-1"
          flat
          @click.native="denyAction">No</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang='ts'>
import Vue from 'vue';
// import { Component, Prop } from 'vue-property-decorator';
import { Component } from 'vue-property-decorator';

@Component
export default class ConfirmLeaveDialog extends Vue {
    // @Prop({ default: 'Are you sure to delete this job?' })
    // question: any;

    answer: Function = () => false;
    active: boolean = false;
    question: string = '';
    text: string = '';

    pop(question: string, text: string): Promise<boolean> {
      this.text = text;
      this.question = question;
      this.active = true;

      return new Promise((resolve: Function, reject: Function) => {
        this.answer = resolve;
      });
    }

    confirmAction(): void {
      this.active = false;
      this.answer(true);
    }

    denyAction(): void {
      this.active = false;
      this.answer(false);
    }
}
</script>

```