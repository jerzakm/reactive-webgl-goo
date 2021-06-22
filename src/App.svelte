<script>
  import ShaderBackground from './ShaderBackground.svelte'
</script>

<ShaderBackground />

<main>
  <side>
    <h2>Purple is pretty</h2>
    <h2>Lorem is old&💀</h2>
    <h2>Wu-Tang is forever</h2>
    <h2>C.R.E.A.M.</h2>
  </side>
  <about>
    <h1>I just can't help it.</h1>
    <h2>Playing with shaders is cool</h2>
  </about>
  <side>
    <h2>Right side represent</h2>
    <h2>Big 🍆</h2>
    <h2>🐒 power</h2>
    <h2>🥑 are overrated</h2>
  </side>
</main>

<style>
  about {
    border: 3px solid white;
    padding: 0rem 1rem;
  }
  h1 {
    font-size: 5rem;
  }
  h2 {
    font-size: 3rem;
  }
  main {
    color: white;
    z-index: 1;
    display: flex;
    align-items: center;
    justify-content: space-around;
    justify-items: center;
    width: 100%;
    height: 100%;
  }
  side {
    height: 80%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    justify-items: center;
  }
</style>
