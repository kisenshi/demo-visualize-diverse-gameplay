<script>
    import VideoPlayer from 'svelte-video-player';
    import { Col, Container, Row } from 'sveltestrap';

    export let agentStats = '';
    export let videoUrl = '';
    export let gamePoster = '';

    function playVideo(event) {
		alert(event.detail.text);
	}
</script>
<Container>
    {#if agentStats}
        <Row>
            <Col lg="4">
                {agentStats}
                <span>Controls Height</span>
                <span>Thumb Size</span>
                <span>Track Height</span>
                <span>Border Radius</span>
                <span>Skip Seconds</span>
            </Col>
            <Col lg="8">
                <VideoPlayer poster={gamePoster} source={videoUrl}/>
            </Col>
        </Row>
    {/if}
</Container>

<style>
    span {
        display: block;
        padding: 5px;
    }
</style>