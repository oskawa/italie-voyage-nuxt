<template>
  <div>
    <header>
      <div class="container">
        <div class="row text-end">
          <div class="col-12">
            <ul>
              <li>
                <NuxtLink to="/"><p>Accueil</p></NuxtLink>
              </li>
              <li>
                <NuxtLink to="/interactive-map"
                  ><p>Carte Interactive</p></NuxtLink
                >
              </li>
            </ul>
          </div>
        </div>
      </div>
    </header>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss">
#__layout {
  position: relative;
}
header {
  padding-top: 1rem;
  z-index: 10;
  position: absolute;
  width: 100%;
  left: 0;
  top: 0;
  a {
    text-decoration: none;
   
  }
  p {
    color: white;
  }
  ul{
      list-style: none;
      padding:0;
      display: inline-flex;
      li:first-child{
          margin-right: 1rem;
      }
  }
}
</style>