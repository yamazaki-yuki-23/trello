<template>
    <div class="list">
        <div class="listheader">
            <p class="list-title">{{ title }}</p>
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
        }
    }
</script>