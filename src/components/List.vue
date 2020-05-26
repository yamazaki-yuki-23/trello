<template>
    <div class="list">
        <div class="listheader">
            <div>
                <input v-if="!editMode" type="text" class="edit-title" @click="changeEditMode" :value="title">
                <input v-else type="text" class="input-list" v-model="listTitle" @focusout="changeEditMode">
            </div>
            <div class="deletelist" @click="removeList">×</div>
        </div>
        <draggable group="cards" :list="cards" @end="$emit('change')">
            <card v-for="(item, index) in cards"
                :body="item.body"
                :key="item.id"
                :cardIndex="index"
                :listIndex="listIndex"
                :card="item"
            />
            <hr>
        </draggable>
        <card-add :listIndex="listIndex" />
    </div>
</template>

<script>
    import draggable from 'vuedraggable'
    import CardAdd from './CardAdd'
    import Card from './Card'

    export default {
        components: {
            CardAdd,
            Card,
            draggable
        },
        props: {
            title: {
                type: String,
                required: true
            },
            cards: {
                type: Array,
                required: true
            },
            listIndex: {
                type: Number,
                required: true
            }
        },
        data() {
            return {
                editMode: false,
                listTitle: this.title
            }
        },
        watch: {
            listTitle() {
                this.$store.dispatch('editList', { title: this.listTitle, listIndex: this.listIndex })
            }
        },
        methods: {
            removeList() {
                this.$confirm('本当にこのリストを削除しますか？',{
                    confirmButtonText: 'OK',
                    cancelButtonText: 'Cancel',
                    type: 'warning'
                }).then(() => {
                    this.$message({
                        type: 'success',
                        message: '削除されました'
                    });
                    this.$store.dispatch('removelist', { listIndex: this.listIndex })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: 'キャンセルされました'
                    });
                });
            },
            changeEditMode() {
                this.editMode = !this.editMode
            }
        }
    }
</script>