<script>
    export let name;
    import { onMount } from "svelte";
    import { Button, Col, Row } from "sveltestrap";
    let count = 0;
    let diff = 0;
    let cps = 0;
    let clicker = 0;
    let gpu = 0;
    let factory = 0;
    let cost = {
        clicker: 10,
        gpu: 100,
        factory: 1000,
    };
    const values = {
        clicker: 0.5,
        gpu: 1,
        factory: 3,
    };

    onMount(() => {
        // CPS
        setInterval(() => {
            cps = count - diff;
            diff = count;
        }, 1000);

        setInterval(() => {
            count =
                count +
                clicker * values.clicker +
                gpu * values.gpu +
                factory * values.factory;
            // console.log(cost);
        }, 1000);
    });

    const clickedItem = (item) => {
        console.log(item);
        switch (item) {
            case "clicker":
                clicker++;
                count = count - cost.clicker;
                cost.clicker = Math.round(
                    cost.clicker + Math.pow(1.15, clicker)
                );
                break;

            case "gpu":
                gpu++;
                count = count - cost.gpu;
                cost.gpu = Math.round(cost.gpu + Math.pow(1.2, gpu));
                break;

            case "factory":
                factory++;
                count = count - cost.factory;
                cost.factory = Math.round(
                    cost.factory + Math.pow(1.25, factory)
                );

                break;
        }
    };
</script>

<svelte:head>
    <link
        rel="stylesheet"
        href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css"
    />

    <link
        rel="stylesheet"
        href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css"
    />
</svelte:head>

<main>
    <Row>
        <Col>
            <h2>
                Coins per Second: {cps}
            </h2>
        </Col>
    </Row>
    <Row>
        <Col>
            <Button color="primary" on:click={() => count++}>{count}</Button>
        </Col>
        <Col>
            <Row>
                <Col>Clicker (+{values.clicker} cps) ({clicker} total)</Col>
                <Col>
                    <Button
                        disabled={cost.clicker > count ? "disabled" : undefined}
                        on:click={() => clickedItem("clicker")}
                        outline
                        color="primary">{cost.clicker}</Button
                    >
                </Col>
            </Row>
            <Row>
                <Col>GPU (+{values.gpu} cps) ({gpu} total)</Col>
                <Col>
                    <Button
                        disabled={cost.gpu > count ? "disabled" : undefined}
                        on:click={() => clickedItem("gpu")}
                        outline
                        color="secondary">{cost.gpu}</Button
                    >
                </Col>
            </Row>
            <Row>
                <Col>Factory (+{values.factory} cps) ({factory} total)</Col>
                <Col>
                    <Button
                        disabled={cost.factory > count ? "disabled" : undefined}
                        on:click={() => clickedItem("factory")}
                        outline
                        color="warning">{cost.factory}</Button
                    >
                </Col>
            </Row>
        </Col>
    </Row>
</main>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
    }

    h1 {
        color: #ff3e00;
        text-transform: uppercase;
        font-size: 4em;
        font-weight: 100;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }
</style>
