<template>
    <div id="solve">
        <div class="sigo_container" v-for="item in items">
            <div class="solve_title">
                <div class="solve_num">
                    {{item.num}} 번 -
                </div>
                <div class="solve_name">
                    {{item.name}} 문제를 풀어봅시다
                </div>
            </div>
            <div class="solve_sidebar">
                <div class="sidebar_title">
                    <h3>문제 설명</h3>
                </div>
                <div class="solve_explain">
                    {{item.explanation}}
                </div>
                <div class="solve_inputex">
                    {{item.inputex}}
                </div>
                <div class="solve_outputex">
                    {{item.outputex}}
                </div>
            </div>
            <div class="solve_main">
                <div class="solve_input">
                    <monaco-editor height="600" language="typescript" :code="code" :editorOptions="options" @mounted="onMounted" @codeChange="onCodeChange">
                    </monaco-editor>
                </div>
                <div class="solve_footer">
                    <div class="solve_output">
                        실행 결과가 이곳에 나타납니다<br>
                        <pre>{{code}}</pre>
                    </div>
                    <div class="solve_button">
                        <div class="solve_run">
                            실행하기
                        </div>
                        <div class="solve_reset">
                            초기화하기
                        </div>
                        <div class="solve_submit" @click="codeSubmit">
                            제출하기
                        </div>
                    </div>
                </div>

            </div>
        </div>

    </div>

</template>
<script>
    import MonacoEditor from 'vue-monaco-editor'

    export default {
        name: 'solve',
        components: {
            'monaco-editor' : MonacoEditor
        },
        data () {
            return {
                url: '',
                items: [],
                code: '// Type away! \n',
                options: {
                    selectOnLineNumbers: false
                }
            }
        },
        created(){
            this.solveMenu = true;
            if(this.$cookie.get('userToken') == null){
                alert('로그인 해주세요');
                location.href="/";
            }
            this.url = this.$route.params.num;
            this.problemData();
            console.log(this.items);

        },
        destroy(){
            this.solveMenu = false;
        },
        methods:{
            onMounted (editor) {
                this.editor = editor
            },
            onCodeChange (editor) {
                console.log(editor.getValue())
            },
            codeEnter(){
                this.code += '\n'
            },
            codeSubmit(){
              alert(this.code);
            },
            inputCode(e){
                this.code = e.target.value;
            },
            problemData: function () {
                this.$http.get('../api/problems/'+this.url)
                    .then((res) => {
                        this.items.push({
                            num : res.data.problem.num,
                            name : res.data.problem.problemName,
                            source : res.data.problem.source,
                            explanation : res.data.problem.explanation,
                            inputex : res.data.problem.problemData.inputExample,
                            outputex : res.data.problem.problemData.outputExample,
                            time : res.data.problem.problemData.timeLimit,
                            memorylimit : res.data.problem.problemData.memoryLimit
                        });
                    })
            }
        }
    }

</script>
<style src="../../assets/css/solve.css" scoped></style>
<style scoped>

</style>
