<template>
<div>
<Hero/>

<Discover-explication/>
<Direct-access/>
</div>

</template>

<script>
export default {
    head() {
    return {
      title: "Voyage en Italie - Accueil",
      meta: [
        {
          name: "description",
          content:
            "Photos et récits de notre voyage en Italie, vous y trouverez en plus des tirages à la vente.",
          hid: "description",
        },
      ],
    };
  },
}
</script>
