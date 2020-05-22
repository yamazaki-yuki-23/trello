<template>
    <div class="card" :style="{border:bodyColor}">
        <el-button type="primary" class="close-button" icon="el-icon-edit" size="mini" circle @click="dialogFormVisible = true"></el-button>
        <div class="body">{{ card.body }}</div>
        <el-dialog title="タスク編集" :visible.sync="dialogFormVisible" class="modal-title" :show-close="false" :close-on-click-modal="textCheck">
            <el-button type="danger" class="task-delete" @click="removeCardFromList">削除</el-button>
            <el-form :model="card" :rules="rules" ref="card">
                <el-form-item label="タイトル" :label-width="formLabelWidth" prop="body">
                    <el-input v-model="card.body" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="詳細" :label-width="formLabelWidth">
                    <el-input
                        type="textarea"
                        :rows="5"
                        v-model="card.description">
                    </el-input>
                </el-form-item>
                <el-form-item label="終了日" :label-width="formLabelWidth">
                    <div class="block">
                        <el-date-picker
                            v-model="card.date"
                            type="date"
                            placeholder="年/月/日"
                            :picker-options="pickerOptions">
                        </el-date-picker>
                    </div>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="cancel('card')">キャンセル</el-button>
                <el-button type="primary" @click="saved('card')" :disabled="!textCheck">保存</el-button>
            </span>
        </el-dialog>
    
    </div>
</template>

<script>
    export default {
        props: {
            body: {
                type: String,
                required: true
            },
            listIndex: {
                type: Number,
                required: true
            },
            cardIndex: {
                type: Number,
                required: true
            },
            card: {
                required: false
            }
        },
        data() {
            return {
                dialogFormVisible: false,
                modalState: false,
                oldBody: this.body,
                oldDescription: this.card.description,
                oldDate: this.card.date,
                formLabelWidth: '120px',
                description: '',
                pickerOptions: {
                    shortcuts: [{
                        text: '今日',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '昨日',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '1週間前',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                date: '',
                rules: {
                    body: [
                        { required: true, message: '必須項目です', trigger: 'blur'}
                    ]
                }
            }
        },
        computed: {
            textCheck() {
                return this.card.body.trim().length > 0 ? true : false
            },
            bodyColor() {
                var now = new Date()
                var ms = new Date(this.card.date).getTime() - now.getTime();
                var days = Math.round(ms / (1000*60*60*24));
                console.log(days)
                if(days < 0) {
                    return "3px solid #ccc"
                } else if(days < 3) {
                    return "3px solid red"
                } else {
                    return "white" 
                }
            }
        },
        methods: {
            removeCardFromList() {
                if(confirm('本当にこのカードを削除しますか？')) {
                    this.$store.dispatch('removeCardFromList', { cardIndex: this.cardIndex, listIndex: this.listIndex })
                    this.dialogFormVisible = false
                }
            },
            saved(card) {
                this.$refs[card].validate((valid) => {
                    if (valid) {
                        this.$store.dispatch('saved', {
                            listIndex: this.listIndex, cardIndex: this.cardIndex, body: this.card.body, description: this.card.description, date: this.card.date 
                        })
                        this.dialogFormVisible = false
                        this.oldBody = this.card.body
                        this.oldDescription = this.card.description
                        this.oldDate = this.card.date
                    } else {
                        return false;
                    }
                });
            },
            cancel() {
                this.card.body = this.oldBody
                this.card.description = this.oldDescription
                this.card.date = this.oldDate
                this.dialogFormVisible = false
            }
        },
    }
</script>