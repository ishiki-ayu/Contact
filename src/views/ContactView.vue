<template>
    <div id="app" class="contact">
        <h2>お問い合わせ</h2>
        <form class="contact-form" method="post" action="">
            <table>
                <tbody>
                    <tr>
                        <th>氏名</th>
                        <td>
                            <input v-model="name" placeholder="田中 太郎">
                            <p class="error">{{ errors.name }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>フリガナ</th>
                        <td>
                            <input v-model="kana" placeholder="タナカ タロウ">
                            <p class="error">{{ errors.kana }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>性別</th>
                        <td>
                          <p calss="select">
                            <select v-model="gender">
                            <option value="3">-</option>
                            <option value="0">男性</option>
                            <option value="1">女性</option>
                            <option value="2">無回答</option>
                          </select>
                            <p class="error">{{ errors.gender }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>生年月日</th>
                        <td>
                            <input v-model="birth" placeholder="19990101">
                            <p class="error">{{ errors.birth }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>郵便番号</th>
                        <td>
                            <input v-model="post" placeholder="0101234">
                            <p class="error">{{ errors.post }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>住所</th>
                        <td>
                            <input v-model="adress" placeholder="〇〇県〇〇市△△町1-1-1 〇〇コーポ101">
                            <p class="error">{{ errors.adress }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>電話番号</th>
                        <td>
                            <input v-model="tell" placeholder="0120000000">
                        </td>
                    </tr>
                    <tr>
                        <th>メールアドレス</th>
                        <td>
                            <input v-model="email" placeholder="info@example.co.jp">
                            <p class="error">{{ errors.email }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>問い合わせの種類</th>
                        <td>
                          <p calss="select">
                            <select v-model="kind">
                            <option value="5">-</option>
                            <option value="0">ご質問</option>
                            <option value="1">ご意見ご要望</option>
                            <option value="2">資料請求</option>
                            <option value="3">ご依頼</option>
                            <option value="4">その他</option>
                          </select>
                            <p class="error">{{ errors.kind }}</p>
                        </td>
                    </tr>
                    <tr>
                        <th>相談内容</th>
                        <td>
                            <input v-model="message">
                            <p class="error">{{ errors.message }}</p>
                        </td>
                    </tr>
                </tbody>
            </table>
            <button v-if="!valid" :disabled="!valid" type="button">未入力の必須項目があります</button>
            <button v-else-if="valid" type="button" @click="submit">送信</button>
        </form>
        <transition name="fade">
            <div v-if="result" class="contact-result">{{ result }}</div>
        </transition>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    data: function() {
        return {
            name: '',
            kana: '',
            birth: '',
            post: '',
            adress: '',
            tell: '',
            email: '',
            message: '',
            result: '',
            gender: '3',
            kind: '5'
        }
    },
    computed: {
        checkName: function(){
            if(!this.name){
                return 'お名前を入力してください';
            }
            return '';
        },
        checkKana: function(){
            if(!this.kana){
                return 'フリガナを入力してください';
            } else if(!this.validKatakana(this.kana)){
                return 'フリガナをカタカナで入力してください';
            }
            return '';
        },
        checkGender: function(){
            if(this.gender === '3'){
                return '性別を選択してください';
            }
            return '';
        },
        checkBirth: function(){
            if(!this.birth){
                return '生年月日を選択してください';
            } else if(!this.validBirth(this.birth)){
                return '生年月日を選択してください';
            }
            return '';
        },
        checkPost: function(){
            if(!this.post){
                return '郵便番号を入力してください';
            } else if(!this.validPost(this.post)){
                return '郵便番号を正しく入力してください';
            }
            return '';
        },
        checkAdress: function(){
            if(!this.adress){
                return '住所を入力してください';
            }
            return '';
        },
        checkEmail: function(){
            if(!this.email){
                return '';
            } else if(!this.validEmail(this.email)){
                return 'メールアドレスを正しく入力してください';
            }
            return '';
        },
        checkKind: function(){
            if(this.kind === '5'){
                return '問い合わせ種類を選択してください';
            } 
            return '';
        },
        checkMessage: function(){
            if(!this.message){
                return '相談内容を入力してください';
            }
            return '';
        },
        errors: function() {
            const errors = {
                'name': this.checkName,
                'kana': this.checkKana,
                'gender': this.checkGender,
                'birth': this.checkBirth,
                'post': this.checkPost,
                'adress': this.checkAdress,
                'email': this.checkEmail,
                'kind': this.checkKind,
                'message': this.checkMessage,
            };
            for (var key in errors) {
                if (errors[key] === '' || errors[key] === null || errors[key] === undefined) {
                    delete errors[key];
                }
            }
            return errors;
        },
        valid: function() {
            return !Object.keys(this.errors).length;
        }
    },
    methods: {
        submit: async function(){
            const result = await this.send();
            this.result = result;
            if(result === '送信完了'){
                this.clear();
            }
        },
        send: async function(){
            const url = 'http://localhost/contact/send.php';
            const params = {
                'name': this.name,
                'kana': this.kana,
                'gender': this.gender,
                'birth': this.birth,
                'post': this.post,
                'adress': this.adress,
                'tell': this.tell,
                'email': this.email,
                'kind': this.kind,
                'message': this.message
            }
            return await axios
                .post(url, params)
                .then(function(res){
                    return res.data;
                })
                .catch(function(error){
                    console.log(error);
            });
        },
        validKatakana: function(kana) {
            const re = /^[ァ-ンｧ-ﾝ\x20\u3000ﾞﾟ]*$/;
            return re.test(kana);
        },
        validBirth: function(birth) {
            const re = /^([0-9]{8})$/;
            return re.test(birth);
        },
        validPost: function(post) {
            const re = /^([0-9]{7})$/;
            return re.test(post);
        },
        validEmail: function(email) {
            const re = /^[a-zA-Z0-9_+-]+(.[a-zA-Z0-9_+-]+)*@([a-zA-Z0-9][a-zA-Z0-9-]*[a-zA-Z0-9]*\.)+[a-zA-Z]{2,}$/;
            return re.test(email);
        },
        clear: function(){
            this.name = '';
            this.kana = '';
            this.gender = '';
            this.birth = '';
            this.post = '';
            this.adress = '';
            this.tell= '';
            this.email = '';
            this.kind = '';
            this.message = '';
        }
    }
}
</script>

<style scoped>
div, p, h2, ul, li {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
.contact {
    width: 100%;
    max-width: 600px;
    padding: 28px;
    margin: 0 auto;
    background-color: #eeeeee;
}
.contact h2 {
    margin: 0 0 28px 0;
}
.contact-form table {
    width: 100%;
    margin: 0 0 28px 0;
    border-collapse: collapse;
}
.contact-form table tr {
    border-bottom: 2px solid #cccccc;
}
.contact-form table tbody tr th {
    width: 25%;
    padding: 20px 10px;
    text-align: left;
}
.contact-form table tbody tr td {
    width: 75%;
    padding: 20px;
    text-align: left;
}
.contact-form table tbody tr td input {
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
}
.contact-form .error {
    margin: 0;
    color: #ff0000;
    font-weight: bold;
    font-size: .85rem;
}
.contact-form button {
    display: block;
    width: 300px;
    padding: 10px 0;
    margin: 0 auto;
    color: #ffffff;
    border: 0;
    box-shadow: none;
    background-color: #384878;
    cursor: pointer;
}
.contact-form button:disabled {
    background-color: #a5a5a5;
}
.contact-result {
    padding: 5px 0;
    margin: 28px auto 0 auto;
    color: #ffffff;
    box-sizing: border-box;
    background-color: #00c2bc;
}
.fade-enter-active, .fade-leave-active {
    transition: opacity .7s;
}
.fade-enter, .fade-leave-to {
    opacity: 0;
}
</style>
