import discord
from discord.ext import commands
from discord.ext.commands import has_permissions
import time
import datetime
import asyncio

intents = discord.Intents.default()
intents.members = True
intents.messages = True

client = commands.Bot(command_prefix= "!", intents=intents)
client.remove_command("help")

@client.event
async def on_ready():
    print("OnLine")
    await client.change_presence(activity=discord.Game(name="!rules | Subscribe: https://www.youtube.com/channel/UCWhz-yF9-oXdlEZIInrCqxA"))

@client.command()
async def rules(ctx):
    embed=discord.Embed(title="SERVER RULES", description="Ignorance of the regulations does not exempt you from complying with the rules on the server.", color=0x00ff00,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    embed.set_author(name="Adielo", url="https://www.youtube.com/channel/UCWhz-yF9-oXdlEZIInrCqxA")
    embed.add_field(name="Warn for:", value="Spam, Flood, CapsLock and Admin Spoofing,", inline=False)
    embed.add_field(name="Mute for:", value="NSFW, Invitations to your Discord server, Tocix and Ping", inline=False)
    embed.add_field(name="Ban for:", value="Big spam, Big flood, Ping @everyone / @here / @<administration role>", inline=False)
    embed.add_field(name=" More info:", value="↓", inline=False)
    embed.add_field(name="1 Warn: ", value="Nothing", inline=True)
    embed.add_field(name="2 Warns:", value="Mute 30m", inline=True)
    embed.add_field(name="3 Warns:", value="Mute 3h", inline=True)
    embed.add_field(name="4 Warns:", value="Mute 24h", inline=True)
    embed.add_field(name="5 Warns:", value="Ban 7d", inline=True)
    embed.add_field(name="6 Warns:", value="TemBan", inline=True)
    embed.set_footer(text="The command will be deleted in 30 seconds.")
    await ctx.channel.send(embed=embed, delete_after = 30)

@client.command()
@has_permissions(ban_members=True)
async def ban(ctx, member : discord.Member, reason="For no reason"):
    await member.ban(reason=reason)
    embed = discord.Embed(title="Banned", description=f"Banned: {member.mention}, Reason: {reason}", color=0xff0000,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    embed.set_author(name=ctx.author.name,icon_url= ctx.author.avatar_url)
    embed.set_footer(text="Banned successively")
    await ctx.channel.send (embed=embed)




@client.command()
@has_permissions(manage_roles = True)
async def mute(ctx, member : discord.Member=None, time=None ,*,reason="For no reason"):

    log_channel = ctx.guild.get_channel(852876803956932622)

    if member == None:
        return await ctx.send(f"Please enter **user**")  

    if time == None:
        return await ctx.send(f"Please enter **time**")


    time_convert = {"s":1, "m":60, "h":3600, "d": 86400}
    time = int(time[0]) * time_convert[time[-1]] 
    role = discord.utils.get(ctx.guild.roles, id = 852876803525443616) 

    embed = discord.Embed(title= f"Muted", description=f"Muted: {member.mention}, Reason {reason}", color=0Xff0000,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    embed.add_field(name="**Administrator**", value=f"<@{ctx.author.id}>", inline=True)
    embed.add_field(name="**Muted**", value=f"{member.mention}", inline=True)
    embed.add_field(name="**Reason**", value=f"{reason}", inline=True)
    embed.set_footer(text=f"Muted on {time}")
    embed.set_author(name=ctx.author.name,icon_url= ctx.author.avatar_url)


    log_em = discord.Embed(title=f"{member}",description=f"**Mute** \n`📄`**Reason:** {reason} \n`🔇`**Muted on** {time} \n`📱`**ID:** {member.id}",color=0xff0000,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    log_em.set_thumbnail(url=member.avatar_url)
    log_em.set_footer(text=ctx.author,icon_url= ctx.author.avatar_url)


    await ctx.channel.send(embed=embed)
    await member.add_roles(role, reason=reason)


    await log_channel.send(embed=log_em)



        
    if role in member.roles:

        await asyncio.sleep(time)
    await member.remove_roles(role)
    reason="The silence time has elapsed"
    log_em = discord.Embed(title=f"{member}",description=f"**Unmute** \n`📄`**Reason:** {reason} \n`🔉`**Unmute after:** {time} \n`📱`**ID:** {member.id}",color=0xff0000)
    log_em.set_thumbnail(url=member.avatar_url)
    log_em.set_footer(text=client.user, icon_url=client.user.avatar_url)
    await log_channel.send(embed=log_em)
    embed = discord.Embed(title= f"Unmuted", description=f"Unmuted: {member.mention}, Reason {reason}", color=0Xff0000,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    embed.add_field(name="**Administrator**", value=client.user.mention, inline=True)
    embed.add_field(name="**Unmuted**", value=f"{member.mention}", inline=True)
    embed.add_field(name="**Reason**", value=f"{reason}", inline=True)
    embed.set_footer(text=f"Unmuted after {time}")
    embed.set_author(name=member.name,icon_url= member.avatar_url)
    await member.send(embed=embed)



@client.command()
@has_permissions(kick_members=True)
async def kick(ctx, member : discord.Member, reason="For no reason"):
    await member.kick(reason=reason)
    embed = discord.Embed(title="Kicked", description=f"Kicked: {member.mention}, Reason {reason}", color=0x00ffff,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    embed.set_author(name=ctx.author.name,icon_url= ctx.author.avatar_url)
    embed.set_footer(text="Kicked successively")
    await ctx.channel.send (embed=embed)

@client.event
async def on_member_join(member):
    channel = discord.utils.get(member.guild.channels, id=852876803525443624)
    embed = discord.Embed(title="Hello in World of AD-s", description=f"Welcome ||{member.mention}|| 🥳", color=0x00ff00,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
    await channel.send(embed=embed)


@client.command()
@has_permissions(manage_messages=True)
async def clear(ctx, value=5):
        await ctx.channel.purge(limit=value + 1)
        embed = discord.Embed(title="Deleted", description=f"Deleted **{value}** messenge/s", color=0x00ffff,timestamp= datetime.datetime.now() -datetime.timedelta(hours=2))
        embed.set_author(name=ctx.author.name,icon_url= ctx.author.avatar_url)
        embed.set_footer(text="The command will be deleted in 20 seconds.")
        message = await ctx.send(embed=embed, delete_after=20)


client.run("ODUyOTQxNjE3NDUwMTg4ODEw.YMOJyA.3qUtph8ScpjPNChocXjzO2wsJlU")
