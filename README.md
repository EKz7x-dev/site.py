# site.py


           import flet as ft
import json
import os

ARQUIVO = "alunos.json"


# =========================
# FUNÇÕES DE ARMAZENAMENTO
# =========================

def carregar_dados():
    if os.path.exists(ARQUIVO):
        with open(ARQUIVO, "r", encoding="utf-8") as arquivo:
            return json.load(arquivo)
    return []


def salvar_dados():
    with open(ARQUIVO, "w", encoding="utf-8") as arquivo:
        json.dump(alunos, arquivo, ensure_ascii=False, indent=4)


# Lista que armazena os alunos
alunos = carregar_dados()


# =========================
# APLICATIVO
# =========================

def main(page: ft.Page):

    page.title = "CRUD - Cadastro de Alunos"
    page.padding = 20
    page.scroll = ft.ScrollMode.AUTO

    # Campos do formulário
    campo_nome = ft.TextField(
        label="Nome",
        width=300
    )

    campo_matricula = ft.TextField(
        label="Matrícula",
        width=200
    )

    campo_curso = ft.TextField(
        label="Curso",
        width=300
    )

    mensagem = ft.Text()

    # ID do aluno que está sendo editado
    aluno_editando = None

    # =========================
    # TABELA
    # =========================

    tabela = ft.DataTable(
        columns=[
            ft.DataColumn(ft.Text("ID")),
            ft.DataColumn(ft.Text("Nome")),
            ft.DataColumn(ft.Text("Matrícula")),
            ft.DataColumn(ft.Text("Curso")),
            ft.DataColumn(ft.Text("Ações")),
        ],
        rows=[]
    )

    # =========================
    # ATUALIZAR TABELA
    # =========================

    def atualizar_tabela():

        tabela.rows.clear()

        for aluno in alunos:

            tabela.rows.append(
                ft.DataRow(
                    cells=[
                        ft.DataCell(
                            ft.Text(str(aluno["id"]))
                        ),

                        ft.DataCell(
                            ft.Text(aluno["nome"])
                        ),

                        ft.DataCell(
                            ft.Text(aluno["matricula"])
                        ),

                        ft.DataCell(
                            ft.Text(aluno["curso"])
                        ),

                        ft.DataCell(
                            ft.Row([
                                ft.IconButton(
                                    icon=ft.Icons.EDIT,
                                    tooltip="Editar",
                                    on_click=lambda e, a=aluno:
                                        editar_aluno(a)
                                ),

                                ft.IconButton(
                                    icon=ft.Icons.DELETE,
                                    tooltip="Excluir",
                                    icon_color=ft.Colors.RED,
                                    on_click=lambda e, a=aluno:
                                        excluir_aluno(a)
                                )
                            ])
                        )
                    ]
                )
            )

        page.update()

    # =========================
    # CREATE
    # =========================

    def cadastrar_aluno(e):

        nonlocal aluno_editando

        nome = campo_nome.value.strip()
        matricula = campo_matricula.value.strip()
        curso = campo_curso.value.strip()

        if nome == "" or matricula == "" or curso == "":
            mensagem.value = "Preencha todos os campos."
            mensagem.color = ft.Colors.RED
            page.update()
            return

        # UPDATE
        if aluno_editando is not None:

            aluno_editando["nome"] = nome
            aluno_editando["matricula"] = matricula
            aluno_editando["curso"] = curso

            mensagem.value = "Aluno atualizado com sucesso!"
            mensagem.color = ft.Colors.GREEN

            aluno_editando = None

            botao_salvar.text = "Cadastrar aluno"

        # CREATE
        else:

            novo_id = 1

            if len(alunos) > 0:
                novo_id = max(
                    aluno["id"]
                    for aluno in alunos
                ) + 1

            novo_aluno = {
                "id": novo_id,
                "nome": nome,
                "matricula": matricula,
                "curso": curso
            }

            alunos.append(novo_aluno)

            mensagem.value = "Aluno cadastrado com sucesso!"
            mensagem.color = ft.Colors.GREEN

        salvar_dados()

        limpar_campos()
        atualizar_tabela()

    # =========================
    # READ
    # =========================

    # A leitura é realizada através
    # da função atualizar_tabela()

    # =========================
    # UPDATE
    # =========================

    def editar_aluno(aluno):

        nonlocal aluno_editando

        aluno_editando = aluno

        campo_nome.value = aluno["nome"]
        campo_matricula.value = aluno["matricula"]
        campo_curso.value = aluno["curso"]

        botao_salvar.text = "Salvar alterações"

        mensagem.value = "Editando aluno..."
        mensagem.color = ft.Colors.BLUE

        page.update()

    # =========================
    # DELETE
    # =========================

    def excluir_aluno(aluno):

        alunos.remove(aluno)

        salvar_dados()

        mensagem.value = "Aluno excluído com sucesso!"
        mensagem.color = ft.Colors.GREEN

        atualizar_tabela()

    # =========================
    # LIMPAR CAMPOS
    # =========================

    def limpar_campos(e=None):

        nonlocal aluno_editando

        aluno_editando = None

        campo_nome.value = ""
        campo_matricula.value = ""
        campo_curso.value = ""

        botao_salvar.text = "Cadastrar aluno"

        page.update()

    # =========================
    # BOTÕES
    # =========================

    botao_salvar = ft.ElevatedButton(
        text="Cadastrar aluno",
        icon=ft.Icons.ADD,
        on_click=cadastrar_aluno
    )

    botao_limpar = ft.OutlinedButton(
        text="Limpar",
        icon=ft.Icons.CLEAR,
        on_click=limpar_campos
    )

    # =========================
    # INTERFACE
    # =========================

    page.add(

        ft.Text(
            "Sistema de Cadastro de Alunos",
            size=30,
            weight=ft.FontWeight.BOLD,
            color=ft.Colors.BLUE
        ),

        ft.Text(
            "CRUD utilizando Flet + Python + JSON",
            size=16
        ),

        ft.Divider(),

        ft.Row([
            campo_nome,
            campo_matricula,
            campo_curso
        ]),

        ft.Row([
            botao_salvar,
            botao_limpar
        ]),

        mensagem,

        ft.Divider(),

        ft.Text(
            "Alunos cadastrados",
            size=22,
            weight=ft.FontWeight.BOLD
        ),

        ft.Row(
            [tabela],
            scroll=ft.ScrollMode.AUTO
        )
    )

    atualizar_tabela()


ft.app(target=main) 