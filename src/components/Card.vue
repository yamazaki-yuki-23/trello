<template>
    <div class="card" :style="{ border: bodyBorder, backgroundColor: card.color }">
        <el-button type="primary" class="close-button" icon="el-icon-edit" size="mini" circle @click="dialogFormVisible = true"></el-button>
        <div class="body">{{ card.body }}</div>
        <p class="time">{{ changeDateType }}</p>
        <el-dialog title="タスク編集" :visible.sync="dialogFormVisible" class="modal-title" :show-close="false" :close-on-click-modal="textCheck" :before-close="handleClose" width="60%">
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
                            format="yyyy/MM/dd"
                            :picker-options="pickerOptions"
                            style="max-width: 100%;">
                        </el-date-picker>
                    </div>
                </el-form-item>
                <el-form-item label="テーマ" :label-width="formLabelWidth">
                    <el-select v-model="value" placeholder="選択する" @change="preview">
                        <el-option
                            v-for="item in colorOptions"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                            style="width:100%;">
                        </el-option>
                    </el-select>
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
                oldBody: this.card.body,
                oldDescription: this.card.description,
                oldDate: this.card.date,
                oldColor: this.card.color,
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
                },
                colorOptions: [{
                    value: '#FADBDA',
                    label: 'レッド'
                }, {
                    value: '#FFE6C1',
                    label: 'オレンジ'
                }, {
                    value: '#FFFC7F',
                    label: 'イエロー'
                }, {
                    value: '#B6FFB5',
                    label: 'グリーン'
                }, {
                    value: '#B5E2FF',
                    label: 'スカイブルー'
                }, {
                    value: '#D1D1D1',
                    label: 'グレー'
                }, {
                    value: '#FFFFFF',
                    label: 'ホワイト'
                }],
                value: this.card.color
            }
        },
        computed: {
            textCheck() {
                return this.card.body.trim().length > 0 ? true : false
            },
            bodyBorder() {
                var now = new Date()
                var ms = new Date(this.card.date).getTime() - now.getTime();
                var days = Math.round(ms / (1000*60*60*24));
                if(days < 0) {
                    return "3px solid #ccc"
                } else if(days < 3) {
                    return "3px solid red"
                } else {
                    return "white" 
                }
            },
            changeDateType() {
                if(this.card.date){
                    var year = new Date(this.card.date).getFullYear()
                    var month = new Date(this.card.date).getMonth() + 1;
                    var day = new Date(this.card.date).getDate();
                    var formatDay = year+"/"+month+"/"+day;
                    return formatDay
                } else {
                    return ""
                }
            }
        },
        methods: {
            removeCardFromList() {
                this.$confirm('本当にこのカードを削除しますか?', {
                    confirmButtonText: 'OK',
                    cancelButtonText: 'Cancel',
                    type: 'warning'
                }).then(() => {
                    this.$message({
                        type: 'success',
                        message: '削除されました'
                    });
                    this.$store.dispatch('removeCardFromList', { cardIndex: this.cardIndex, listIndex: this.listIndex })
                    this.dialogFormVisible = false
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: 'キャンセルされました'
                    });
                });
            },
            saved(card) {
                this.$refs[card].validate((valid) => {
                    if (valid) {
                        this.$store.dispatch('saved', {
                            listIndex: this.listIndex, cardIndex: this.cardIndex, body: this.card.body, description: this.card.description, date: this.card.date, color: this.value 
                        })
                        this.oldBody = this.card.body
                        this.oldDescription = this.card.description
                        this.oldDate = this.card.date
                        this.oldColor = this.value
                        this.dialogFormVisible = false
                    } else {
                        return false;
                    }
                });
            },
            cancel() {
                this.card.body = this.oldBody
                this.card.description = this.oldDescription
                this.card.date = this.oldDate
                this.card.color = this.value =  this.oldColor
                this.dialogFormVisible = false
            },
            handleClose() {
                this.card.body = this.oldBody
                this.card.description = this.oldDescription
                this.card.date = this.oldDate
                this.card.color = this.value = this.oldColor
                this.dialogFormVisible = false
            },
            preview(){
                this.card.color = this.value
            }
        },
    }
</script>