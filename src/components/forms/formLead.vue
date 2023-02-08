<template>
    <form @submit.prevent="onSumbit">
        <div v-if="outputAlert.success" class="alert alert-success" role="alert">{{ outputAlert.message }}</div>
        <div v-if="outputAlert.failed" class="alert alert-danger" role="alert">{{ outputAlert.message }}</div>
        <div class="form-group">
            <label for="form-firstName">Имя</label>
            <input class="form-control" id="form-firstName" v-model="form.firstName" v-on:blur="v$.form.firstName.$touch()">
            <span v-if="v$.form.firstName.$error">
                <template v-if="v$.form.firstName.required.$invalid">
                    Пожалуйста, укажите имя
                </template>
                <template v-if="v$.form.firstName.maxLength.$invalid">
                    Длина имени не должна превышать {{ v$.form.firstName.maxLength.$params.max }} символов
                </template>
            </span>
        </div>
        <div class="form-group">
            <label for="form-email">Почта</label>
            <input class="form-control" id="form-email" v-model="form.email" v-on:blur="v$.form.email.$touch()">
            <span v-if="v$.form.email.$error">
                <template v-if="v$.form.email.required.$invalid">
                    Пожалуйста, укажите почту
                </template>
                <template v-else>
                    Неправильный формат почты
                </template>
            </span>
        </div>
        <div class="form-group">
            <label for="form-telephone">Телефон</label>
            <input class="form-control" id="form-telephone" v-model="form.telephone" v-on:blur="v$.form.telephone.$touch()">
            <span v-if="v$.form.telephone.$error">
                <template v-if="v$.form.telephone.required.$invalid">
                    Пожалуйста, укажите телефон
                </template>
                <template v-else>
                    Неправильный формат телефона
                </template>
            </span>
        </div>
        <div class="form-group">
            <label for="form-price">Цена</label>
            <input class="form-control" id="form-price" v-model="form.price" v-on:blur="v$.form.price.$touch()">
            <span v-if="v$.form.price.$error">
                <template v-if="v$.form.price.required.$invalid">
                    Пожалуйста, укажите цену
                </template>
                <template v-else>
                    Некорректная цена
                </template>
            </span>
        </div>
        <div class="d-flex flex-row">
            <button type="sumbit" class="btn btn-primary mr-2 mb-2">Отправить</button>
            <button type="cancel" class="btn btn-secondary mr-2 mb-2" disabled>Отмена</button>
        </div>
    </form>
</template>
<script>
import { useVuelidate } from '@vuelidate/core'
import { required, minLength, maxLength } from '@vuelidate/validators'

export default {
    setup: () => ({ v$: useVuelidate() }),
    data() {
        return {
            form: {
                firstName: null,
                email: null,
                telephone: null,
                price: null
            },
            outputAlert: {
                success: null,
                failed: null,
                message: null
            }
        }
    },
    validations() {
        return {
            form: {
                firstName: {
                    required,
                    minLength: minLength(2),
                    maxLength: maxLength(100)
                },
                email: {
                    required,
                    validFormat: value => /@.+\./.test(value)
                },
                telephone: {
                    required,
                    validFormat: value => /^(\+?7|8)?9\d{9}$/.test(value)
                },
                price: {
                    required,
                    validFormat: value => /^\+?\d+$/.test(value)
                }
            }
        }
    },
    methods: {
        formMessage(type, message) {
            switch (type) {
                case 'success':
                    this.outputAlert.message = message;
                    this.outputAlert.success = true;
                    this.outputAlert.failed = (this.outputAlert.failed ? false : null);
                    break;
                case 'fail':
                    this.outputAlert.message = message;
                    this.outputAlert.success = (this.outputAlert.success ? false : null);
                    this.outputAlert.failed = true;
                    break;
            };
        },
        getFormData(object) {
            return Object.keys(object).reduce((formData, key) => {
                formData.append(key, object[key]);
                return formData;
            }, new FormData());
        },
        async onSumbit() {
            this.v$.$validate();
            if (!(!this.v$.$error)) {
                return;
            };

            let url = 'https://browserbotdetection.ru/api/leads/create/';
            let formData = this.getFormData(this.form);

            let response = await this.sendRequest(url, formData);
            if (!(response)) {
                this.formMessage('fail', 'Внутренняя ошибка: обновите страницу и попробуйте еще раз');
            };
            switch (response.status) {
                case 'ok':
                    this.formMessage('success', 'Сделка оформлена, номер: ' + response.message);
                    break;
                default:
                    this.formMessage('fail', 'Внутренняя ошибка: попробуйте позднее');
                    break;
            };
        },
        sendRequest: async (url, formData) => {
            try {
                let response = await fetch(url, { 
                    method: 'POST', 
                    body: formData 
                });
                return (response.ok ? await response.json() : false);
            } catch (err) {
                return false;
            };
        }
    }
}
</script>