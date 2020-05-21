<template>
    <div>
        <el-dialog title="タスク編集" :visible.sync="dialogFormVisible" class="modal-title">
            <el-form :model="form">
                <el-form-item label="タイトル" :label-width="formLabelWidth">
                    <el-input v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="詳細" :label-width="formLabelWidth">
                    <el-input
                        type="textarea"
                        :rows="5"
                        v-model="textarea">
                    </el-input>
                </el-form-item>
                <el-form-item label="日付" :label-width="formLabelWidth">
                    <div class="block">
                        <el-date-picker
                        v-model="value2"
                        type="date"
                        placeholder="年/月/日"
                        :picker-options="pickerOptions">
                        </el-date-picker>
                    </div>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">Cancel</el-button>
                <el-button type="primary" @click="dialogFormVisible = false">Confirm</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        props: ['dialogFormVisible'],
        data() {
        return {
            form: {
                name: '',
                region: '',
                date1: '',
                date2: '',
                delivery: false,
                type: [],
                resource: '',
                desc: '',
            },
            formLabelWidth: '120px',
            textarea: '',
            pickerOptions: {
                // disabledDate(time) {
                //     return time.getTime() > Date.now();
                // },
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
            value2: '',
        }
    },

    }
</script>