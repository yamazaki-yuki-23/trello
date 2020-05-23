<template>
    <div>
        <header>
            My  Sticky
        </header>
        <main>
            <el-button type="info" class="csv-output" size="small" v-on:click="downloadCSV" :disabled="!checkCardCount">カード出力<i class="el-icon-upload el-icon-right"></i></el-button>
            <p class="info-line">カード数: {{ totalCardCount }}</p>
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
        },
        checkCardCount() {
            return this.totalCardCount > 0
        }
    },
    methods: {
        movingCard() {
            this.$store.dispatch('updateList', { lists: this.lists })
        },
        movingList() {
            this.$store.dispatch('updateList', { lists: this.lists })
        },
        downloadCSV () {
            var csv = '\ufeff' + 'リスト,カード,詳細,終了日\n'
            for(var i = 0; i < this.lists.length; i++){
                for(var j = 0; j < this.lists[i]['cards'].length; j++){
                    var card = this.ValidateCard(this.lists[i]['cards'][j])
                    var line = this.lists[i]['title'] + ',' + this.lists[i]['cards'][j]['body'] + ',' + card['discription'] + ',' + card['date'] + '\n'
                    csv += line
                }
            }
            let blob = new Blob([csv], { type: 'text/csv' })
            let link = document.createElement('a')
            link.href = window.URL.createObjectURL(blob)
            link.download = 'trello.csv'
            link.click()
        },
        ValidateCard(card) {
            card['discription'] = card['discription'] ? card['discription'] : ""
            if(card['date']) {
                var year = new Date(card['date']).getFullYear()
                var month = new Date(card['date']).getMonth() + 1;
                var day = new Date(card['date']).getDate();
                var formatDay = year+"/"+month+"/"+day;
                card['date'] = formatDay
            } else {
                card['date'] =  ""
            }
            return card
        }
    }
}
</script>

<style>

</style>