-- OofWooVoidSpam.lua
-- OofWoo’s void spam FULL REWORKED (FAST VERSION)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- FILE SYSTEM
local folder = "CoordConfigs"
local configFile = folder .. "/configs.json"
local autoloadFile = folder .. "/autoload.txt"

if makefolder and not isfolder(folder) then
    makefolder(folder)
end

local configs = {}

local function loadFromFile()
    if isfile and isfile(configFile) then
        configs = HttpService:JSONDecode(readfile(configFile))
    end
end

local function saveToFile()
    if writefile then
        writefile(configFile, HttpService:JSONEncode(configs))
    end
end

local function saveAutoload(name)
    if writefile then
        writefile(autoloadFile, name)
    end
end

local function loadAutoload()
    if isfile and isfile(autoloadFile) then
        return readfile(autoloadFile)
    end
end

loadFromFile()

-- UI LIB
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()

local Options = Library.Options
local Toggles = Library.Toggles

local Window = Library:CreateWindow({
    Title = "OofWoo’s Void Spam",
    Size = UDim2.fromOffset(600, 460),
})

Library.ToggleKey = nil

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.UserInputType == Enum.UserInputType.Keyboard then
        if input.KeyCode == Enum.KeyCode.LeftAlt then
            Library:Toggle()
        end
    end
end)

local VoidSpamTab = Window:AddTab("Void Spam")
local SettingsTab = Window:AddTab("Settings")

-- STATE
local isActive = true
local values = {}
local AXES = {"X","X-","X+","Y","Y-","Y+","Z","Z-","Z+"}
for _,k in ipairs(AXES) do values[k]=0 end

-- TELEPORT
local function doTeleport()
    if not isActive then return end
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local pos = hrp.Position

    local px = values["X"] ~= 0 and values["X"] or pos.X
    px = px + values["X+"] - values["X-"]

    local py = values["Y"] ~= 0 and values["Y"] or pos.Y
    py = py + values["Y+"] - values["Y-"]

    local pz = values["Z"] ~= 0 and values["Z"] or pos.Z
    pz = pz + values["Z+"] - values["Z-"]

    hrp.CFrame = CFrame.new(px, py, pz)
end

-- UI
local VoidBox = VoidSpamTab:AddLeftGroupbox("Void Spam")

VoidBox:AddToggle("TeleportEnabled", {
    Text = "Enable Teleport",
    Default = true,
    Callback = function(val)
        isActive = val
    end
})

-- CONNECTIONS
local voidLoopRunning = false
local randomConn

local function stopRandom()
    if randomConn then
        randomConn:Disconnect()
        randomConn = nil
    end
end

-- ⚡ VOID SPAM (ULTRA FAST)
VoidBox:AddToggle("VoidSpamEnabled", {
    Text = "Enable Void Spam",
    Callback = function(val)
        voidLoopRunning = val

        if val then
            stopRandom()

            task.spawn(function()
                while voidLoopRunning do
                    if not isActive then 
                        task.wait()
                        continue 
                    end

                    local char = player.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")

                    if hrp then
                        hrp.CFrame = CFrame.new(hrp.Position.X, -500, hrp.Position.Z)
                    end

                    task.wait(0.001) -- 🔥 speed
                end
            end)
        end
    end
})

-- RANDOM TELEPORT
VoidBox:AddToggle("RandomTeleport", {
    Text = "Random Teleport",
    Default = false,
    Callback = function(val)
        voidLoopRunning = false
        stopRandom()

        if val then
            isActive = true

            randomConn = RunService.Heartbeat:Connect(function()
                local char = player.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end

                for _,axis in ipairs(AXES) do
                    values[axis] = math.random(0, 1000000)

                    if Options["Slider_"..axis] then
                        Options["Slider_"..axis]:SetValue(values[axis])
                    end
                end

                doTeleport()
            end)
        end
    end
})

-- SLIDERS
local function createSlider(axis)
    VoidBox:AddSlider("Slider_"..axis, {
        Text = axis,
        Min = 0,
        Max = 1000000,
        Default = 0,
        Rounding = 0,
        Callback = function(val)
            values[axis] = val
            doTeleport()
        end
    })
end

for _,axis in ipairs(AXES) do
    createSlider(axis)
end

-- SETTINGS
local SettingsBox = SettingsTab:AddLeftGroupbox("Configs")

local selectedConfigIndex
local configNameValue = ""

SettingsBox:AddInput("ConfigNameInput", {
    Text = "Config Name",
    Callback = function(val)
        configNameValue = val
    end
})

SettingsBox:AddDropdown("ConfigList", {
    Text = "Saved Configs",
    Values = {},
    Callback = function(val)
        for i,cfg in ipairs(configs) do
            if cfg.name == val then
                selectedConfigIndex = i
            end
        end
    end
})

local function refreshDropdown()
    local names = {}
    for _,cfg in ipairs(configs) do
        table.insert(names,cfg.name)
    end
    Options.ConfigList:SetValues(names)
end

SettingsBox:AddButton({
    Text = "+ Create Config",
    Func = function()
        local name = configNameValue ~= "" and configNameValue or ("Config "..#configs+1)
        local snapshot = {}
        for _,k in ipairs(AXES) do snapshot[k]=values[k] end

        table.insert(configs,{name=name,data=snapshot})
        saveToFile()
        refreshDropdown()

        Library:Notify("Saved "..name,3)
    end
})

SettingsBox:AddButton({
    Text = "Load Selected",
    Func = function()
        local cfg = configs[selectedConfigIndex]
        if not cfg then return end

        for _,k in ipairs(AXES) do
            values[k]=cfg.data[k] or 0
            if Options["Slider_"..k] then
                Options["Slider_"..k]:SetValue(values[k])
            end
        end

        doTeleport()
        Library:Notify("Loaded "..cfg.name,3)
    end
})

SettingsBox:AddButton({
    Text = "Delete Selected",
    Func = function()
        if not selectedConfigIndex then return end

        local name = configs[selectedConfigIndex].name
        table.remove(configs,selectedConfigIndex)

        selectedConfigIndex=nil
        saveToFile()
        refreshDropdown()

        Library:Notify("Deleted "..name,2)
    end
})

SettingsBox:AddDivider()

SettingsBox:AddToggle("AutoLoadToggle", {
    Text = "Auto Load Last Config",
    Default = true
})

SettingsBox:AddButton({
    Text = "Set Selected as Auto Load",
    Func = function()
        local cfg = configs[selectedConfigIndex]
        if not cfg then return end
        saveAutoload(cfg.name)
        Library:Notify("Autoload set: "..cfg.name,3)
    end
})

SettingsBox:AddToggle("HideUI", {
    Text = "Hide UI on Execute",
    Default = false
})

local function loadConfigByName(name)
    for _,cfg in ipairs(configs) do
        if cfg.name == name then
            for _,k in ipairs(AXES) do
                values[k]=cfg.data[k] or 0
                if Options["Slider_"..k] then
                    Options["Slider_"..k]:SetValue(values[k])
                end
            end
            doTeleport()
            return true
        end
    end
end

task.delay(1,function()
    if Toggles.AutoLoadToggle and Toggles.AutoLoadToggle.Value then
        local name = loadAutoload()
        if name then
            if loadConfigByName(name) then
                Library:Notify("Auto Loaded "..name,3)
            end
        end
    end

    if Toggles.HideUI and Toggles.HideUI.Value then
        Library:Toggle()
    end
end)

ThemeManager:SetLibrary(Library)
ThemeManager:ApplyToTab(SettingsTab)
