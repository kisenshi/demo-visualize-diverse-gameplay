<script>
    import { slide } from 'svelte/transition';
    import { Container } from 'sveltestrap';
    import { Card, CardBody } from 'sveltestrap';

    export let title;

    let openDetails = false;
    const toggleComponent = () => openDetails = !openDetails;
</script>

<Card>
    <Container>
        <button class="accordion" on:click={toggleComponent} aria-expanded={openDetails}>
            <svg style="tran"  width="20" height="20" fill="none" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" viewBox="0 0 24 24" stroke="currentColor">
                <path d="M9 5l7 7-7 7"></path>
            </svg> {title}
        </button>
        {#if openDetails}
            <div transition:slide={{ duration: 300 }} class="information">
                <CardBody>
                    <slot></slot>
                </CardBody>
            </div>
        {/if}
    </Container>
</Card>

<style>
    .information {
        text-align: left;
    }

    .accordion {
        border: none; 
        background: none;
        display:block; 
        color: inherit; 
        cursor: pointer; 
        margin: 0; 
        padding-bottom: 0.5em; 
        padding-top: 0.5em
    }
    
    svg {
        transition: transform 0.2s ease-in;
    }
    
    [aria-expanded=true] svg { transform: rotate(0.25turn); }
</style>