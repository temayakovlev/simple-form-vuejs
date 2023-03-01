<script setup>
import createLead from './components/forms/createLead.vue';
</script>

<template>
    <div class="container-scroller">
        <main>
            <div class="container-fluid page-body-wrapper">
                <div class="main-panel">
                    <div class="content-wrapper">
                        <div class="container">
                            <div class="row">
                                <div class="col-lg-12 grid-margin stretch-card">
                                    <div class="card">
                                        <div class="card-body">
                                            <h4 class="card-title">Создание сделки в AmoCRM</h4>
                                            <div class="col-md-6">
                                                <createLead />
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <footer class="footer">
                    <div class="d-sm-flex justify-content-center justify-content-sm-between">
                        <span class="text-muted text-center text-sm-left d-block d-sm-inline-block">Создано на фреймворке Vue.js 3</span>
                        <span class="float-none float-sm-right d-block mt-1 mt-sm-0 text-center">2023</span>
                    </div>
                </footer>
            </div>
        </main>
    </div>
</template>