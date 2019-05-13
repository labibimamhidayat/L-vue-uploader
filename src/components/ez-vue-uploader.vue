<template>
    <div class="ez-vue-uploader dis-flex flex-col">
        <div class="dis-flex upload-file">
            <input type="file" ref="file" v-multiple="maxFiles" style="display : none;" @change="onFilePicked">
            <div @click="clickSpan" :style="`min-height: ${minHeightUploadSpan};`" :class="`upload-span ${customClass.uploadSpan || ''} ${!withButtonUpload?'handPart':''}`">
                <span v-if="nameFile">{{nameFile}}</span>
                <span v-else-if="!loading">{{textUploadSpan}}</span>
                <div class="dis-flex flex-col upload-loading" v-else>
                    <span class="my-1">Uploading ...</span>
                </div>
            </div>
            <div v-if="!loading && withButtonUpload" :class="`action-button`">
                <button :class="`upload-btn ${customClass.actionButton || ''}`" @click="pickFile">Upload File</button>
            </div>
        </div>
        <div v-if="successMsg" class="msg-success">{{successMsg}}</div>
        <div v-if="errorMsg" class="msg-error">{{errorMsg}}</div>
        <div v-if="customMessage" class="msg-upload">File Hanya Boleh {{customMessage}}*</div>
        <div v-if="fileSizeMessage" class="msg-upload">Maksimum Ukuran File Adalah {{fileSizeMessage}}*</div>
    </div>
</template>
<script>
export default {
    data(){
        return {
            uploadFilesModel : null,
            errorMsg : '',
            loading : false,
            nameFile : '',
            successMsg : ''
        }
    },
    props : {
        type : {
            type : String,
            default : ''
        },
        typeAllowed : {
            type : Array,
            default : ()=>[]
        },
        sizeFile : {
            type : Object,
            default : ()=>{return {}}
        },
        withButtonUpload : {
            type : Boolean,
            default : false
        },
        minHeightUploadSpan : {
            type : String,
            default : ''
        },
        textUploadSpan : {
            type : String,
            default : 'Upload File Here'
        },  
        customClass : {
            type : Object,
            default : ()=>{return {}}
        },
        maxFiles : {
            type : Number,
            default : 1
        }
    },
/*     watch : {
        config(val){

        }
    }, */
    computed : {
        customMessage(){
            if(this.typeAllowed.length){
                return this.typeAllowed.reduce((accum,accul,index)=>{
                    if(index){
                        return `${accum} / ${accul.key}`
                    }else{
                        return `${accum} ${accul.key}`
                    }
                },'')
            }else{
                return ''
            }  
        },
        fileSizeMessage(){
            if(Object.keys(this.sizeFile).length){
                return this.sizeFile.size + ' ' + this.sizeFile.key
            }else {
                return ''
            }
        }
    },
    directives : {
        multiple : {
            bind(el,bind,vnode){
                if(bind.value > 1){
                    console.log('aosdksaodksao');
                    el.setAttribute('multiple','multiple')
                }
            }
        }
    },
    created(){
    },
    methods : {
        emitToParent(val){
            this.$emit('input',val)
        },
        onFilePicked(event){
            const files = event.target.files[0]
            const namaFile = files.name
            const size = files.size
            const type = files.type
            console.log(URL.createObjectURL(files));
            console.log('ini panjang',event.target.files.length);
            /* const error = this.validationFile({namaFile,size,type})
            if(error){
                this.setErrorMsg(error)
            }else{
                this.uploadFiles(files)
            } */
        },
        setErrorMsg(msg){
            this.errorMsg = msg
            setTimeout(()=>{
                this.errorMsg = ''
            }, 5000);
        },
        setSuccessMsg(msg){
            this.successMsg = msg
            setTimeout(()=>{
                // body
                this.successMsg = ''
            }, 5000);
        },
        validationFile(data){
            if(this.typeAllowed.length){
                const sizeValidation = this.typeAllowed.findIndex((dataArray)=>{
                    if(dataArray.type == data.type){
                        return dataArray
                    }
                })
                if(sizeValidation == -1){
                    return 'Tipe file yang anda upload salah'
                }
            }else if (Object.keys(this.sizeFile).length){
                if((this.sizeFile.key == 'MB' && this.sizeFile.size < data.size/1024/1024) || (this.sizeFile.key == 'KB' && this.sizeFile.size < data.size/1024)){
                    return 'File yang anda upload tidak boleh lebih dari '+this.sizeFile.size
                }else{
                    return ''
                }
            }else{
                return ''
            }
        },
        async uploadFiles(files){
            let formData = new FormData()
            formData.append('file',files)
            formData.append('type',this.type)
            if(!this.loading){
                this.resetInstance()
                this.loading = true
                try {
                    const result = await this.uploadFilesModel.uploadFile(formData)
                    this.nameFile = files.name
                    this.setSuccessMsg('File Berhasil Diupload')
                    this.emitToParent(result.data.data.path)
                } catch (error) {
                   /*  console.log('ini error',error); */
                } finally {
                    this.loading = false
                }
            }
        },
        pickFile(){
            this.$refs.file.click()
        },
        clickSpan(){
            if(!this.withButtonUpload){
                this.pickFile()
            }
        },
        resetInstance(){
            this.errorMsg = ''
            this.nameFile = ''
            this.successMsg = ''
            this.emitToParent('')
        }
    }
}
</script>
<style lang="scss" scoped>
    .handPart{
        cursor: pointer;
    }
    .upload-file {
        .upload-span{
            min-height: 100px;
            width: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            border: 1px solid #d9d9d9;
            .upload-loading{
                width: 100%;
                align-items: center;
            }
        }
        .upload-btn{
            margin: 0px 2px;
        }
    }
    .upload-btn {
        &:hover {
            background:-webkit-gradient(linear, left top, left bottom, color-stop(0.05, #378de5), color-stop(1, #79bbff));
            background:-moz-linear-gradient(top, #378de5 5%, #79bbff 100%);
            background:-webkit-linear-gradient(top, #378de5 5%, #79bbff 100%);
            background:-o-linear-gradient(top, #378de5 5%, #79bbff 100%);
            background:-ms-linear-gradient(top, #378de5 5%, #79bbff 100%);
            background:linear-gradient(to bottom, #378de5 5%, #79bbff 100%);
            filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#378de5', endColorstr='#79bbff',GradientType=0);
            background-color:#378de5;
        }
        &:active {
            position:relative;
            top:1px;
        }
        &:focus {
            outline: unset;
        }
        -moz-box-shadow:inset 0px 1px 0px 0px #bbdaf7;
        -webkit-box-shadow:inset 0px 1px 0px 0px #bbdaf7;
        box-shadow:inset 0px 1px 0px 0px #bbdaf7;
        background:-webkit-gradient(linear, left top, left bottom, color-stop(0.05, #79bbff), color-stop(1, #378de5));
        background:-moz-linear-gradient(top, #79bbff 5%, #378de5 100%);
        background:-webkit-linear-gradient(top, #79bbff 5%, #378de5 100%);
        background:-o-linear-gradient(top, #79bbff 5%, #378de5 100%);
        background:-ms-linear-gradient(top, #79bbff 5%, #378de5 100%);
        background:linear-gradient(to bottom, #79bbff 5%, #378de5 100%);
        filter:progid:DXImageTransform.Microsoft.gradient(startColorstr='#79bbff', endColorstr='#378de5',GradientType=0);
        background-color:#79bbff;
        -moz-border-radius:6px;
        -webkit-border-radius:6px;
        border-radius:6px;
        border:1px solid #84bbf3;
        display:inline-block;
        cursor:pointer;
        color:#ffffff;
        font-family:Arial;
        font-size:15px;
        font-weight:bold;
        padding:6px 24px;
        text-decoration:none;
        text-shadow:0px 1px 0px #528ecc;
    }
    .msg-upload {
        color: grey;
    }
    .msg-error {
        color: red;
    }
    .msg-success {
        color: lawngreen;
    }
</style>
