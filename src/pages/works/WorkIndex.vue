<template>
  <div class="container">
        <h2>Encore-Work List</h2>
        <!-- 검색기능 추가 -->
        <input  type="text"
                placeholder="Search...."
                class="form-control"
                v-model="searchText">
        <hr/>

        <!-- form -> WorkForm.vue 이동 -->
        <WorkForm @add-work="addWork" />

        <!-- 페이지 로딩시 추가되는 코드  -->
        <!-- <div v-if="!works.length" class="error">
            등록한 일정이 존재하지 않습니다.
        </div> -->

        <div class="error">  
            {{ error }}
        </div>

        <!-- 검색기능 추가하는 서치 바 -->
        <div v-if="!filterWorks.length" class="error"> 
            필터링 결과가 존재하지 않습니다.
        </div>    

        <WorkList   :works="filterWorks" 
                    @toggle-work="toggleWork"
                    @delete-work="deleteWork" /> 
      
  
    </div>
    
</template>


<script>
import { ref , computed } from 'vue';
import axios from "@/axios" ;

// form 
import WorkForm  from "@/components/WorkForm.vue" ; 
// list
import WorkList  from "@/components/WorkList.vue" ; 
export default {
    components : {
        WorkForm,
        WorkList
    },
    setup() {
        const works      = ref([]);
        const searchText = ref(''); 
        const error      = ref('');

        // 통신이전 Ver
        // const addWork = (work) => {
        //     console.log(">>>>>>>>>> emit work  , " , work); 
        //     works.value.push(work); 
        // }
        
        const addWork = async (work) => {
            error.value = '';
            try {
                const res = await axios.post('works', {
                id : Date.now(),
                subject : work.subject ,
                completed : false
                });
                works.value.push(res.data);                 
            } catch(err) {
                error.value = "Something to Wrong";
            }
        }

        // index - 배열인덱스 , 인덱스에 해당하는 객체의 id로 삭제
        // axios.delete();
        const deleteWork = async (index) => {
            error.value = '' ;
            const work_id = works.value[index].id ;  
            try{
                await axios.delete(`works/${work_id}`);
                works.value.splice(index, 1);
            } catch(err) {
                console.log(">> deletework err , " , err);
                error.value = "Something to Wrong";
            }
            

        }

        const toggleWork = async (index , checked ) => {
            console.log(">>>>>> WorkIndex.vue toggleWork checked , " , checked );
            error.value = '' ; 
            try{
                const work_id = works.value[index].id ; 
                await axios.patch(`works/${work_id}` , {
                    completed : !works.value[index].completed  
                }) ;
                works.value[index].completed =  checked ; 
            
            } catch(err) {
                console.log(">> deletework err , " , err);
                error.value = "Something to Wrong";
            }
        }
        // javascript - filter() , includes()
        const filterWorks = computed(() => {
            if(searchText.value) {
                return works.value.filter(work =>{
                    return work.subject.includes(searchText.value) ; 
                })
            }
            return works.value ; 

        }) ; 
            
        // 전체데이터 가져오기위한 통신 .... 
        const getWorks = async () => {
            try{
                const res = await axios.get(`works`);
                works.value = res.data ; 
            }catch(err) {
                console.log(">> getWorks err , " , err);
                error.value = "Something to Wrong";
            }
        }
        //////////
        getWorks();
        ////////////
        return {
            works,
            deleteWork,
            addWork,
            toggleWork,
            searchText,
            filterWorks,
            error
        }
    }
    
}
</script>

<style>
  .error {
    color : red ; 
  }

  .work {
    color : green ;
    text-decoration: line-through ; 
  }
</style>