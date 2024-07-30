

<svelte:head>
    <title>Emoji Game</title>
</svelte:head>
<style>
    :global(body){
        font-family: 'Roboto', sans-serif;
        margin:0;
        padding:0;
        display:flex;
        flex-direction: column;
        justify-content:center;
        align-items:center;
        height:100vh;
        background-color:#fff4e0;
    }
    #wrap {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1;
  text-align: center;
  align-content: center;
}

footer {
  padding: 1rem;
  text-align: center;
}
   
    
  

</style>
<div id="wrap">
    <main>
        <slot/>
    </main>
    <footer><p>&copy; Lionel ABATAN. Tous droits réservés.</p></footer>
</div>  