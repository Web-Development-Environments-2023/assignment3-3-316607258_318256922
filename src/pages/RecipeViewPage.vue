<template>
  <div class="container">
    <div v-if="recipe">
      <div class="recipe-header mt-3 mb-4">
        <h1>{{ recipe.title }}</h1>
        <img :src="recipe.image" class="center" />
      </div>
      <div class="recipe-body">
        <div class="wrapper">
          <div class="wrapped">
            <div class="mb-3">
              <div>Ready in {{ recipe.readyInMinutes }} minutes</div>
              <div>Likes: {{ recipe.popularity }} likes</div>
            </div>
            Ingredients:
            <ul>
              <li
                v-for="(r, index) in recipe.ingredients"
                :key="index + '_' + r.id"
              >
                {{ r.amount }} {{ r.unit }} {{ r.name }}
              </li>
            </ul>
          </div>
          <div class="wrapped">
            Instructions:
            <!-- <ol>
              <li v-for="s in recipe._instructions" :key="s.number">
                {{ s.step }}
              </li>
            </ol> -->
            <p> {{recipe.instructions}}</p>
          </div>
        </div>
      </div>
      <!-- <pre>
      {{ $route.params }}
      {{ recipe }}
    </pre> -->
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      recipe: null
    };
  },

  async created() {
    try {
      let response;
      // response = this.$route.params.response;

      try {
        response = await this.axios.get(
          // "https://test-for-3-2.herokuapp.com/recipes/info",
          this.$root.store.server_domain + "/recipes/" +  this.$route.params.recipeId,
        );

        // console.log("response.status", response.status);
        if (response.status !== 200) this.$router.replace("/NotFound");
      } catch (error) {
        console.log("error.response.status", error.response.status);
        this.$router.replace("/NotFound");
        return;
      }

      let {
        analyzedInstructions,
        instructions,
        ingredients,
        popularity,
        readyInMinutes,
        image,
        title,
        id
      } = response.data;
      console.log("response.data",response.data);
// response.data.recipe;
    let _instructions = instructions;
    // if( analyzedInstructions){
    //   let _instructions = analyzedInstructions
    //     .map((fstep) => {
    //       fstep.steps[0].step = fstep.name + fstep.steps[0].step;
    //       return fstep.steps;
    //     })
    //     .reduce((a, b) => [...a, ...b], []);
    // }


      let _recipe = {
        instructions,
        _instructions,
        analyzedInstructions,
        ingredients,
        popularity,
        readyInMinutes,
        image,
        title,
        id
      };

      this.recipe = _recipe;
    } catch (error) {
      console.log(error);
    }
    if(!(this.$root.store.isWatchedRecipesContainRecepie(this.recipe.id))){
      this.$root.store.addToWatchedRecipes(this.recipe);   
   }
  }
};
</script>

<style scoped>
.wrapper {
  display: flex;
}
.wrapped {
  width: 50%;
}
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 50%;
}
/* .recipe-header{

} */
</style>
