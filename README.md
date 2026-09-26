# site.py

import flet as ft


def main(page: ft.Page):
    page.title = "Demonstração de Componentes Flet (Aulas 3 a 6)"
    page.padding = 20
    page.scroll = ft.ScrollMode.AUTO

    page.add(ft.Text("1. Exemplo: Checkbox", weight=ft.FontWeight.BOLD, size=18))

    cb_man = ft.Checkbox(label="Homem", value=False)
    cb_woman = ft.Checkbox(label="Mulher", value=True)
    text_cb_result = ft.Text()

    def button_cb_clicked(e):
        if cb_man.value and not cb_woman.value:
            text_cb_result.value = "Selecionado: Homem"
        elif cb_woman.value and not cb_man.value:
            text_cb_result.value = "Selecionado: Mulher"
        elif cb_man.value and cb_woman.value:
            text_cb_result.value = "Erro: Ambas as opções não podem ser selecionadas simultaneamente."
        else:
            text_cb_result.value = "Aviso: Nenhuma opção foi selecionada."
        page.update()

    btn_cb = ft.TextButton(text="Confirmar Seleção", on_click=button_cb_clicked)

    page.add(cb_man, cb_woman, btn_cb, text_cb_result)
    page.add(ft.Divider())

    page.add(ft.Text("2. Exemplo: Radio Button", weight=ft.FontWeight.BOLD, size=18))

    text_radio_result = ft.Text()

    def radio_group_changed(e):
        text_radio_result.value = f"Time selecionado: {radio_group.value}"
        page.update()

    radio_group = ft.RadioGroup(
        content=ft.Column(
            controls=[
                ft.Radio(value="Real Madrid", label="Real Madrid", fill_color=ft.Colors.BLUE),
                ft.Radio(value="Juventus", label="Juventus"),
                ft.Radio(value="Arsenal", label="Arsenal"),
            ]
        ),
        on_change=radio_group_changed,
    )

    page.add(
        ft.Text("Escolha seu time de futebol preferido:"),
        radio_group,
        text_radio_result,
    )
    page.add(ft.Divider())

    page.add(ft.Text("3. Exemplo: Switch", weight=ft.FontWeight.BOLD, size=18))

    switch_football = ft.Switch(label="Futebol", value=False)
    switch_basketball = ft.Switch(label="Basquete", value=True)
    text_switch_result = ft.Text()

    def button_switch_clicked(e):
        if switch_football.value and not switch_basketball.value:
            text_switch_result.value = "Esporte preferido selecionado: Futebol"
        elif switch_basketball.value and not switch_football.value:
            text_switch_result.value = "Esporte preferido selecionado: Basquete"
        elif switch_football.value and switch_basketball.value:
            text_switch_result.value = "Erro: Selecione apenas um esporte por vez."
        else:
            text_switch_result.value = "Aviso: Selecione pelo menos um esporte."
        page.update()

    btn_switch = ft.TextButton(text="Verificar Preferência", on_click=button_switch_clicked)

    page.add(
        switch_football,
        switch_basketball,
        btn_switch,
        text_switch_result,
    )
    page.add(ft.Divider())

    
    page.add(ft.Text("4. Exemplo: Dropdown", weight=ft.FontWeight.BOLD, size=18))

    text_dropdown_result = ft.Text()

    def dropdown_changed(e):
        text_dropdown_result.value = f"Opção selecionada no menu: {dd.value}"
        page.update()

    dd = ft.Dropdown(
        hint_text="Escolha um clube",
        width=200,
        options=[
            ft.dropdown.Option("Real Madrid"),
            ft.dropdown.Option("Juventus"),
            ft.dropdown.Option("Arsenal"),
        ],
        on_change=dropdown_changed,
    )

    page.add(dd, text_dropdown_result)


if __name__ == "__main__":
    ft.app(target=main)