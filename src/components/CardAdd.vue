<template>
    <form :class="classList" @submit.prevent="addCardToList">
        <input
            v-model="body"
            type="text"
            class="text-input"
            placeholder="カードを追加する"
            @focusin="startEditing"
            @focusout="finishEditing"
        />
        <button type="submit" class="add-button" v-if="isEditing || bodyExists">
            追加する
        </button>
    </form>
</template>

<script>
    export default {
        props: {
            listIndex: {
                type: Number,
                required: true,
            }
        },
        data() {
            return {
                body: '',
                isEditing: false,
            }
        },
        computed: {
            classList() {
                const classList = ['addcard']
                if (this.isEditing) {
                    classList.push('active')
                }
                if (this.bodyExists) {
                    classList.push('addable')
                }
                return classList
            },
            bodyExists() {
                return this.body.trim().length > 0 ? true : this.bodyReset()
            }
        },
        methods: {
            startEditing() {
                this.isEditing = true
            },
            finishEditing() {
                this.isEditing = false
            },
            addCardToList() {
                this.$store.dispatch('addCardToList', { body: this.body, listIndex: this.listIndex })
                this.body = ''
            },
            bodyReset() {
                this.body = ''
                return false
            }
        },
    }
</script>