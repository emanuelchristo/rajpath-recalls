<script>

</script>

<style>
    input {
        width: 250px;
        font-family: Helvetica;
        font-size: 12pt;
        padding: 16px;
        border-radius: 8px;
        border: none;
        box-shadow: 0 3px 15px rgba(0,0,0,0.12);
    }

    label {
        font-family: Helvetica;
        font-size: 10pt;
        margin-bottom: 5px;
    }
</style>

<div class="container">
    <label><slot/></label>
    <input type="text">
</div>