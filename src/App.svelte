<script>
  import { onMount, onDestroy } from 'svelte';

import LoremIpsum from './LoremIpsum.svelte'

	import Introduction from './Introduction.svelte'

  let activeStepIndex = 0;
  let observer;
  let scrollyRef;
  let flourishID = "2299460"; // L'ID de la story Flourish

// le texte des boites




	
	
  let stepsData = [
    { "text": "En <em>2019</em>, le nombre de spectateurs s'élevait à <mark style='background-color: #4328E7; color:white;padding: 2px; border-radius: 5px;'><strong>	19 877 878 </strong></mark>, marquant ainsi un pic de fréquentation avant les perturbations causées par la pandémie de COVID-19" },
    { "text": "En <em>2020</em> , le nombre de spectateurs a chuté drastiquement à <mark style='background-color: #4328E7; color:white;padding: 2px; border-radius: 5px;'><strong>5 538 934</strong></mark>, reflétant l'impact des restrictions sanitaires liées à la pandémie de COVID-19 sur l'industrie cinématographique belge." },
    { "text": "En <em>2021</em> , le nombre de spectateurs a affiché une légère augmentation pour atteindre <mark style='background-color: #4328E7; color:white;padding: 2px; border-radius: 5px;'><strong>7 453 797</strong></mark>, témoignant d'une timide reprise après les perturbations majeures de l'année précédente." },
		{ "text": "En <em>2022</em> , le nombre de spectateurs a continué de progresser, s'élevant à <mark style='background-color: #4328E7; color:white;padding: 2px; border-radius: 5px;'><strong>13 683 579</strong></mark>, bien qu'il n'ait pas encore atteint les niveaux pré-pandémiques." },
		{ "text": "En résumé, les chiffres montrent une amélioration encourageante pour l'industrie cinématographique belge. " },
  ];

	// Le "moteur" du scrollytelling qui utilise l'Intersection Observer API (en gros, le code observe ce qu'il y a à l'écran)
	// ça on ne touche pas sinon tout se casse !

  onMount(() => {
    observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        const { target, isIntersecting } = entry;
        const index = Array.from(scrollyRef.querySelectorAll('.step')).indexOf(target);
        if (isIntersecting) {
          activeStepIndex = index;
        }
      });
    }, { threshold: 0.6 });

    const stepElements = scrollyRef.querySelectorAll('.step');
    stepElements.forEach(el => observer.observe(el));
  });

  onDestroy(() => {
    observer.disconnect();
  });
</script>

<h1>En 2022, le niveau de fréquentation des cinémas n'a pas retrouvé son niveau d'avant-crise</h1>

<Introduction/>

<!-- si tu affiches la step_0, alors montre la slide_0 -->
<!-- si tu affiches la step_1, alors montre la slide_1 -->
<!-- si tu affiches la step_2, alors montre la slide_2 -->

<section bind:this={scrollyRef} class="section-container">

	  <div class="foreground">
    {#each stepsData as { text }, index}
      <div class="step" data-step={index}>
        <div class="step-content">
          <p>{@html text}</p> <!-- @html important pour que le css du script soit pris en compte-->
        </div>
      </div>
    {/each}
  </div>
	
  <div class="sticky-background">
    <!-- On affiche la slide Flourish en fonction de l'index -->
		<iframe src={`https://flo.uri.sh/story/${flourishID}/embed#slide-${activeStepIndex}`} title="Contenu interactif ou visuel" class="flourish-embed" frameborder="0" scrolling="no" style="width:100%;height:100vh;"></iframe>
  </div>


</section>


<style>

	/* Ici les valeurs pour l'ensemble de la page > 
	peut nécessiter des modifs de couleurs dans Flourish 
	pour s'assurer que le graphe soit tjs bien visible (titre de graphique noir sur
	fond de page noir,ça ne se voit pas bien...*/

	:global(body) {
    background-color: white; 
		color: black;
		font-family: 'Times', sans-serif; 
  }
	
	*{
		box-sizing: border-box;
	}
	.section-container {
    margin-top: 1em;
    text-align: center;
    display: flex;
    flex-direction: row; 
		
  }

  .sticky-background {
    position: sticky;
    top: 0;
    height: 100vh;
    flex: 0 1 60%;
    overflow: hidden;
    z-index: 1;
  }

  .foreground {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 0 1 40%;
		margin-bottom: 100vh;
  }

  .step {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    position: relative;
    z-index: 2;
    width: 100%;
  }

  .step-content {
    background-color: rgba(245, 245, 245, 0.8);
		box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    color: black;
    border-radius: 10px;
		border: 5px solid #a0c8f0; 
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    z-index: 10;
    font-size: 1rem;
    text-align: left;
    width: 100%; 
    max-width: 500px; 
    margin: auto;
  }

	/* Pour adapter la vue en mobile: steps centrées par dessus le graphique */

  @media screen and (max-width: 768px) {
    .section-container {
      flex-direction: column-reverse;
    }
    .sticky-background, .foreground {
      width: 100%; 
    }
    .foreground {
      margin-top: -80vh; 
    }
  }
</style>

