<template>
  <div class="main">
        <h1>Text editor</h1>
        <div class="text">
            <p><strong>INSERT</strong> - add text to the end of the current text, where text consists of at most 20
                English letters</p>
            <p><strong>DELETE</strong> - erase the last character of the current text</p>
            <p><strong>COPY</strong> - copy the substring of the current text starting from index and spanning until
                the end</p>
            <p><strong>PASTE</strong> - add the last copied text to the end of the current text</p>
            <p><strong>UNDO</strong> - undo the last successful INSERT, DELETE or PASTE operation </p>
        </div>
        <div>
            <select v-model="selectVal" >
                <option default disabled value="">Choose option</option>
                <option v-for="option in selOptions" :value="option">{{option}}</option>
            </select>
            <span v-if="selectVal === 'INSERT'">
                <input v-model="inputVal" maxlength="20" type="text">
            </span>
            <span v-if="selectVal === 'COPY'">
                <input class="w70" maxlength="99" v-model="inputVal"  type="number">
            </span>
            <button v-if="selectVal" @click="applyBtn">Apply</button>
        </div>
        <textarea v-model="textareaVal"></textarea>
  </div>

</template>

<script>

export default {
    name: 'App',
    data() {
        return {
            selectVal: '',
            selOptions: ['INSERT', 'DELETE', 'COPY', 'PASTE', 'UNDO'],
            inputVal: '',
            textareaVal: '',
            copiedVal: '',
            undoVal: {
                name: '',
                value: '',
            },
            undoArr: [],
        }
    },
    methods: {
        applyBtn() {
            if(this.selectVal === 'INSERT' && this.inputVal.length) {
                //Вставка текста из строки
                this.textareaVal += this.inputVal;
                this.undoVal = {
                    name: this.selectVal,
                    value: this.inputVal,
                }
            } else if(this.selectVal === 'DELETE' ) {
                //Данные для отмены
                this.undoVal.name = this.selectVal;
                this.undoArr.push(this.textareaVal.slice(-1));
                //Удаление последнего символа строки
                this.textareaVal = this.textareaVal.slice(0, -1);

            } else if(this.selectVal === 'COPY') {
                //Копирование
                const inputValue = +this.inputVal;
                const textLength = this.textareaVal.length;
                const num = inputValue > 0 && inputValue <= textLength ? inputValue -1 : 0;
                this.copiedVal = this.textareaVal.slice(num, textLength);
            } else if(this.selectVal === 'PASTE' && this.copiedVal !== '') {
                //Данные для отмены
                this.undoVal = {
                    name: this.selectVal,
                    value: this.copiedVal,
                };
                //Вставка скопированого текста
                this.textareaVal += this.copiedVal;
            } else if(this.selectVal === 'UNDO') {
                if(this.undoVal.name === 'INSERT') {
                    this.textareaVal = this.textareaVal.replace(this.undoVal.value, '');
                } else if(this.undoVal.name === 'DELETE') {
                    this.textareaVal = this.textareaVal + this.undoArr.slice(-1);
                    this.undoArr.pop();
                } else if(this.undoVal.name === 'PASTE') {
                    this.textareaVal = this.textareaVal.replace(this.undoVal.value, '');
                    this.undoVal.value = '';
                }
            }
            this.inputVal = '';
        },

    },


}
</script>

<style>
    *, :before, :after {
        box-sizing: border-box;
    }
    .w70 {
        width: 70px;
    }

    h1, h2, h3 {
        text-align: center;
        margin-bottom: 100px;
        display: inline-block;
        width: 100%;
    }

    body {
        font-size: 16px;
        font-family: Arial, sans-serif;
    }
    .main {
        max-width: 900px;
        width: 100%;
        margin: 30px auto;
    }

    p {
        margin: 0 0 10px;
    }

    .main div {
        display: flex;
        align-items: center;
    }

    .main .text {
        flex-direction: column;
        align-items: flex-start;
        text-align: left;
        margin-bottom: 30px;
    }


    textarea {
        width: 100%;
        height: 300px;
        padding: 10px;
        resize: none;
        margin-top: 10px;
        font-size: 16px;
    }
    textarea:focus {
        outline: none;
    }

    select {
        height: 40px;
        width: 180px;
        font-size: 16px;
        padding: 5px;
        cursor: pointer;
    }
    input {
        height: 40px;
        width: 300px;
        margin: 0 0 0 10px;
        padding: 5px;
        font-size: 16px;
    }
    button {
        height: 40px;
        font-size: 16px;
        margin-left: 10px;
        padding: 5px 10px;
        cursor: pointer;
    }


</style>
