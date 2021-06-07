<script>

import { Alert } from 'sveltestrap';
import { Col, Container, Row } from 'sveltestrap';
import { Card, CardBody, CardHeader, CardTitle } from 'sveltestrap';
import { Popover, Button } from 'sveltestrap';

export let configInfo;
export let dataInfo;

</script>

<h1>{dataInfo['gameName']}</h1>
<Card>
    <Container>
        <Alert color="info" dismissible>
            Hover over the features, behaviours and game screenshot for more information.
        </Alert>
        <Row>
            <Col lg="6">
                <CardBody>
                    <h2>Features</h2>
                    <Button 
                    color="primary"
                    id="info-{dataInfo["featureX"]}">
                    {configInfo["features"][dataInfo["featureX"]]["name"]}
                    </Button>
                    <Popover
                        trigger="hover"
                        placement="bottom"
                        target="info-{dataInfo["featureX"]}"
                        title="{configInfo["features"][dataInfo["featureX"]]["name"]}">
                        {configInfo["features"][dataInfo["featureX"]]["description"]}
                    </Popover>
                    {" x "}
                    <Button 
                        color="primary"
                        id="info-{dataInfo["featureY"]}">
                        {configInfo["features"][dataInfo["featureY"]]["name"]}
                    </Button>
                    <Popover
                        trigger="hover"
                        placement="bottom"
                        target="info-{dataInfo["featureY"]}"
                        title="{configInfo["features"][dataInfo["featureY"]]["name"]}">
                        {configInfo["features"][dataInfo["featureY"]]["description"]}
                    </Popover>
                </CardBody>
                <CardBody>
                    <h3>Behaviours enabled for agents:</h3>
                    {#each dataInfo['behaviours'] as behaviour }
                        <Button 
                        color="info"
                        id="info-{behaviour}">
                        {configInfo["behaviours"][behaviour]["name"]}
                        </Button>
                        <Popover
                        trigger="hover"
                        placement="bottom"
                        target="info-{behaviour}"
                        title="{configInfo["behaviours"][behaviour]["name"]}">
                        {configInfo["behaviours"][behaviour]["description"]}
                        </Popover>
                    {" "}
                    {/each}
                </CardBody>
            </Col>
            <Col lg="6">
                <CardBody>
                    <img id="img-screenshot" src={dataInfo["gamePoster"]} alt="{configInfo["games"][dataInfo['gameName']]["name"]} screenshot"/>
                    <Popover
                    trigger="hover"
                    placement="bottom"
                    target="img-screenshot"
                    title="{configInfo["games"][dataInfo['gameName']]["name"]}">
                    <div>{configInfo["games"][dataInfo['gameName']]["description"]}</div>
                    </Popover>
                </CardBody>
            </Col>
        </Row>
    </Container>
</Card>

<style>
    img {
		width: 100%;
	}
</style>