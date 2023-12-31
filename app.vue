<template>
  <link
    href="https://cdn.jsdelivr.net/npm/tailwindcss@2.1.2/dist/tailwind.min.css"
    rel="stylesheet"
  />

  <div>
    <div
      v-for="vaga in data"
      class="max-w-5xl w-full m-2 justify-between mx-auto sm:px-6 lg:px-8 flex flex-row bg-white overflow-hidden shadow sm:rounded-lg p-6 items-center align-center"
    >
      <div>
        <h2 class="text-2xl leading-7 font-semibold">{{ vaga.titulo }}</h2>
        <p class="mt- max-w-md text-gray-600">{{ vaga.descricao }}</p>
      </div>

      <p class="text-gray-600">{{ vaga.beneficios }}</p>

      <div>
        <button
          type="button"
          @click="openModal"
          class="inline-flex justify-center rounded-md border border-transparent bg-blue-100 px-4 py-2 text-sm font-medium text-blue-900 hover:bg-blue-200 focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2"
        >
          Cadastrar
        </button>
      </div>
    </div>

    <TransitionRoot appear :show="isOpen" as="template">
      <Dialog as="div" @close="closeModal" class="relative z-10">
        <TransitionChild
          as="template"
          enter="duration-300 ease-out"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="duration-200 ease-in"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <div class="fixed inset-0 bg-black bg-opacity-25" />
        </TransitionChild>

        <div class="fixed inset-0 overflow-y-auto">
          <div
            class="flex min-h-full items-center justify-center p-4 text-center"
          >
            <TransitionChild
              as="template"
              enter="duration-300 ease-out"
              enter-from="opacity-0 scale-95"
              enter-to="opacity-100 scale-100"
              leave="duration-200 ease-in"
              leave-from="opacity-100 scale-100"
              leave-to="opacity-0 scale-95"
            >
              <DialogPanel
                class="w-full max-w-md transform overflow-hidden rounded-2xl bg-white p-6 text-left align-middle shadow-xl transition-all"
              >
                <DialogTitle
                  as="h3"
                  class="text-lg font-medium leading-6 text-gray-900"
                >
                  Cadastrar na Vaga
                </DialogTitle>

                <form class="py-4" @submit.prevent="criaCandidato">
                  <label class="block py-2">
                    <span class="block text-sm font-medium text-slate-700"
                      >Nome</span
                    >
                    <input
                      id="nome"
                      name="nome"
                      v-model="nome"
                      required
                      class="border-zinc-500 border-2 rounded-md w-full p-2 placeholder-slate-400 contrast-more:border-slate-400 contrast-more:placeholder-slate-500"
                    />
                  </label>

                  <label class="block py-2">
                    <span class="block text-sm font-medium text-slate-700"
                      >E-mail</span
                    >
                    <input
                      id="email"
                      type="email"
                      name="email"
                      v-model="email"
                      required
                      class="border-zinc-500 border-2 w-full rounded-md p-2 placeholder-slate-400 contrast-more:border-slate-400 contrast-more:placeholder-slate-500"
                    />
                  </label>

                  <label class="block py-2">
                    <span class="block text-sm font-medium text-slate-700"
                      >Link do Curriculo / Perfil do Linkedin</span
                    >
                    <input
                      id="curriculo"
                      name="curriculo"
                      v-model="curriculo"
                      required
                      class="border-zinc-500 border-2 w-full rounded-md p-2 placeholder-slate-400 contrast-more:border-slate-400 contrast-more:placeholder-slate-500"
                    />
                  </label>

                  <div class="mt-4">
                    <button
                      type="submit"
                      class="inline-flex justify-center rounded-md border border-transparent bg-blue-100 px-4 py-2 text-sm font-medium text-blue-900 hover:bg-blue-200 focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2"
                    >
                      Cadastrar
                    </button>
                  </div>
                </form>
              </DialogPanel>
            </TransitionChild>
          </div>
        </div>
      </Dialog>
    </TransitionRoot>
  </div>
</template>

<script setup>
import { ref } from "vue";
import {
  TransitionRoot,
  TransitionChild,
  Dialog,
  DialogPanel,
  DialogTitle,
} from "@headlessui/vue";

const isOpen = ref(false);

function closeModal() {
  isOpen.value = false;
}
function openModal() {
  isOpen.value = true;
}

const supabase = useSupabaseClient();
const { data } = await supabase.from("vagas").select("*");

const nome = ref("");
const email = ref("");
const curriculo = ref("");

async function criaCandidato() {
  try {
    const candidato = {
      nome: nome.value,
      email: email.value,
      curriculo: curriculo.value,
    };

    let { error } = await supabase.from("candidatos").insert(candidato);
    if (error) throw error;

    closeModal();
  } catch (error) {
    alert(error.message);
  }
}
</script>
