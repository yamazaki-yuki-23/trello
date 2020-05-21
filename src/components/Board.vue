<template>
    <div>
        <header>
            Trello
        </header>
        <main>
            <p class="info-line">タスク数: {{ totalCardCount }}</p>
            <div class="list-index">
                <draggable :list="lists" @end="movingList" class="list-index">
                    <list
                        v-for="(item, index) in lists"
                        :key="item.id"
                        :title="item.title"
                        :cards="item.cards"
                        :listIndex="index"
                        @change="movingCard"
                    />
                </draggable>
                <list-add/>
            </div>
        </main>
    </div>
</template>

<script>
import draggable from 'vuedraggable'
import ListAdd from './LIstAdd'
import List from './List'
import { mapState } from 'vuex'

export default {
    components: {
        ListAdd,
        List,
        draggable
    },
    computed: {
        ...mapState([
            'lists'
        ]),
        totalCardCount() {
            return this.$store.getters.totalCardCount
        }
    },
    methods: {
        movingCard() {
            this.$store.dispatch('updateList', { lists: this.lists })
        },
        movingList() {
            this.$store.dispatch('updateList', { lists: this.lists })
        }
    }
}
</script>

<style>

</style>