<template>
  <div class="box has-background-primary">
    <div class="columns is-multiline is-mobile">
      <div class="column is-12">
        <h2 class="title is-capitalized has-text-white">{{ name }}</h2>
      </div>
      <div class="column is-12">
        <div class="columns is-mobile is-gapless">
          <div
            v-for="img in imgs"
            :key="img.id"
            class="column is-3-desktop is-3-tablet is-4-mobile"
          >
            <figure class="image">
              <img :src="img" class="image is-square" style="padding-top:0px"/>
            </figure>
          </div>
        </div>
      </div>
      <div class="column is-3-desktop is-3-tablet is-6-mobile">
        <h3 class="title is-4 has-text-white">Weight</h3>
        <p class="tag">{{ weight }}</p>
      </div> 
      <div class="column is-3-desktop is-3-tablet is-6-mobile">
        <h3 class="title is-4 has-text-white">Height</h3>
        <p class="tag">{{ height }}</p>
      </div>
      <div class="column is-3-desktop is-3-tablet is-6-mobile">
        <h3 class="title is-4 has-text-white">Abilities</h3>
        <div class="tags">
          <p class="tag" v-for="ability in abilities" :key="ability.id">{{ ability.ability.name }}</p>
        </div>
      </div>
      <div class="column is-3-desktop is-3-tablet is-6-mobile">
        <h3 class="title is-4 has-text-white">Type</h3>
        <div class="tags">
          <p class="tag" v-for="type in types" :key="type.id">{{ type.type.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PokemonCard",
  props: {
    name: {},
    imgs: {},
    height: {},
    weight: {},
    abilities: {},
    types: {}
  }
};
</script>

<style>
</style>