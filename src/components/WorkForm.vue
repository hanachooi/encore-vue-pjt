<template>
    <form>
        <div class="d-flex">
            <div class="flex-grow-1 mr-2">
                <input  type="text" 
                        class="form-control"
                        placeholder="new work...">
            </div>    
            <div>
                <button class="btn btn-primary" type="submit">ADD</button>
            </div> 
        </div>
        <!-- v-show 널값입력시 메시지 출력 -->
        <div class="error">
            This Field can not be empty
        </div>

    </form> 
</template>

<script>
export default {

}
</script>

<style>

</style>