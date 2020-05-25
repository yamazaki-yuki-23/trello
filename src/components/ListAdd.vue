<template>
    <form :class="classList" @submit.prevent="addList">
        <input
            v-model="title" 
            type="text"
            class="text-input add-list"
            placeholder="リストを作成"
            @focusin="startEditing"
            @focusout="finishEditing"
        >
        <button type="submit"
                class="add-button add-list"
                v-if="isEditing || titleExists"
        >
            追加する
        </button>
    </form>
</template>

<script>
    export default {
        data() {
            return {
                title: '',
                isEditing: false,
            }
        },
        computed: {
            classList() {
                const classList = ['addlist']
                if(this.isEditing) {
                    classList.push('active')
                }
                if(this.titleExists) {
                    classList.push('addable')
                }
                return classList
            },
            titleExists() {
                return this.title.trim().length > 0 ? true : this.titleReset()
            },
        },
        methods: {
            addList() {
                this.$store.dispatch('addlist', { title: this.title })
                this.title = ''
            },
            startEditing() {
                this.isEditing = true;
            },
            finishEditing() {
                this.isEditing = false;
            },
            titleReset() {
                this.title = ''
                return false
            }
        },
    }
</script>