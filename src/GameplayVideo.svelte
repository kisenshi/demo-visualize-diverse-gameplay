<script>
    import { Spinner } from 'sveltestrap';
    import { Progress } from 'sveltestrap';
    import { Button } from 'sveltestrap';

    export let loadingVideo;
    export let videoAvailable;
    export let videoSrc;
    export let videoPoster;
   
    let currentTime = 0;
	let duration;
    let paused = true;

    let controlColor = "primary";
    let disablePlayButton = true;

    $: gameplayProgress = currentTime / duration;

    function handlePlay() {
		if (paused) {
			videoPanel.play();
		} else {
			videoPanel.pause();
		}
	}

    function gameplayVideoLoaded() {
        videoPanel.resetTime();
        disablePlayButton = false;
    }

    export const videoPanel = {
        resetTime() {
            this.pause();
            currentTime = 0;
        },
        pause() {
            paused = true
        },
        play() {
            paused = false
        },
    }
</script>

<div class="container">
    {#if loadingVideo}
            <Spinner color="info" type="border" />
    {:else}
        {#if videoAvailable}
            <video
            poster={videoPoster}
            src={videoSrc}
            on:loadeddata={gameplayVideoLoaded}
            bind:currentTime
            bind:duration
            bind:paused>
            <track kind="captions">
            </video>
            
            <div class="videoControl">
                <div class="progressBar">
                    <Progress color={controlColor} value={(gameplayProgress * 100) || 0} />
                </div>
                <div class="controlBtn">
                    <Button color={controlColor} on:click={handlePlay} disabled={disablePlayButton}>
                        {#if paused}
                            Play
                        {:else}
                            Pause
                        {/if}
                    </Button>
                </div>
            </div>
        {:else}
            <p>Video not available</p>
        {/if}
    {/if}
</div>

<style>
    .progressBar{
        margin-bottom: 10px;
    }

	div {
		position: relative;
	}

	video {
		width: 100%;
	}
</style>