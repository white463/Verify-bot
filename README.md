import discord
from discord.ui import View, Button
from discord import ButtonStyle

TOKEN = "your token"
GUILD_ID = 1571643508949778533       # ID твоего сервера
ROLE_ID = 1571650252921114685      # ID роли verified

intents = discord.Intents.default()
intents.members = True             # нужно, чтобы выдавать роли

client = discord.Client(intents=intents)
tree = discord.app_commands.CommandTree(client)


class VerifyView(View):
    def __init__(self):
        # timeout=None — кнопка живёт вечно, не «умирает» после перезапуска бота
        super().__init__(timeout=None)

    @discord.ui.button(label="Верифицироваться", style=ButtonStyle.green, emoji="✅", custom_id="verify_button")
    async def verify(self, interaction: discord.Interaction, button: Button):
        guild = interaction.guild
        role = guild.get_role(ROLE_ID)

        if role is None:
            await interaction.response.send_message("Роль не найдена, напиши админу.", ephemeral=True)
            return

        if role in interaction.user.roles:
            await interaction.response.send_message("Ты уже верифицирован ✅", ephemeral=True)
            return

        await interaction.user.add_roles(role, reason="Верификация по кнопке")
        await interaction.response.send_message(f"Готово! Тебе выдана роль {role.mention}", ephemeral=True)


@tree.command(name="verify_panel", description="Отправить панель верификации")
async def verify_panel(interaction: discord.Interaction):
    await interaction.response.send_message(
        "**Верификация**\nНажми кнопку ниже, чтобы получить доступ к серверу.",
        view=VerifyView()
    )


@client.event
async def on_ready():
    await tree.sync()
    print(f"Бот запущен: {client.user}")


client.run(TOKEN)# Verify-bot
