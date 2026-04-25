local rs = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local lplr = Players.LocalPlayer
local camera = workspace.CurrentCamera

local DrRayLibrary = loadstring(game:HttpGet("https://raw.githubusercontent.com/AZYsGithub/DrRay-UI-Library/main/DrRay.lua"))()
local window = DrRayLibrary:Load("SheScripts Gag", "Midnight")

local trollTab = DrRayLibrary.newTab("Troll", "rbxassetid://4483362458")

local selectedPlr = nil
local viewEnabled = false
local loopSound = false
local soundChoice = "Happy"

local function getPlayerNames()
    local names = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lplr then
            table.insert(names, p.Name)
        end
    end
    return names
end

trollTab.newDropdown("Select Player", "Choose player", getPlayerNames(), function(option)
    selectedPlr = Players:FindFirstChild(option)
    if viewEnabled and selectedPlr and selectedPlr.Character then
        local hum = selectedPlr.Character:FindFirstChildOfClass("Humanoid")
        if hum then camera.CameraSubject = hum end
    end
end)

trollTab.newToggle("View Player", "Toggle camera follow player", false, function(state)
    viewEnabled = state
    if state and selectedPlr and selectedPlr.Character then
        local hum = selectedPlr.Character:FindFirstChildOfClass("Humanoid")
        if hum then camera.CameraSubject = hum end
    else
        local myHum = lplr.Character and lplr.Character:FindFirstChildOfClass("Humanoid")
        if myHum then camera.CameraSubject = myHum end
    end
end)

local function grab()
    rs:WaitForChild("CarryAndThrow"):FireServer(2)
end

local function throw()
    rs:WaitForChild("CarryAndThrow"):FireServer("Throw")
end

trollTab.newButton("Grab Player", "Grab the selected player", function()
    if selectedPlr and selectedPlr.Character and selectedPlr.Character:FindFirstChild("HumanoidRootPart") then
        local char = lplr.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char:MoveTo(selectedPlr.Character.HumanoidRootPart.Position)
            task.wait(0.3)
            grab()
        end
    end
end)

trollTab.newButton("Kill Selected Player", "Grab and throw the selected player", function()
    if selectedPlr and selectedPlr.Character and selectedPlr.Character:FindFirstChild("HumanoidRootPart") then
        local lchar = lplr.Character
        if lchar and lchar:FindFirstChild("HumanoidRootPart") then
            local pos = lchar.HumanoidRootPart.Position
            lchar:MoveTo(selectedPlr.Character.HumanoidRootPart.Position)
            task.wait(0.25)
            grab()
            task.wait(1.5)
            lchar:MoveTo(Vector3.new(99999, 99999, 99999))
            task.wait(0.5)
            throw()
            task.wait(2.25)
            lchar:MoveTo(pos)
        end
    end
end)

local soundOptions = {"Happy", "Angry", "Rich", "Skibidi", "Winner"}

trollTab.newDropdown("Select Sound", "Choose sound", soundOptions, function(option)
    soundChoice = option
end)

trollTab.newToggle("Loop Selected Sound", "Loop the selected sound", false, function(state)
    loopSound = state
    task.spawn(function()
        while loopSound do
            local args
            if soundChoice == "Happy" then
                args = {
                    [1] = 0,
                    [2] = "Happy",
                    [3] = {
                        [1] = 93640466361792,
                        [3] = "\240\159\152\128"
                    }
                }
            elseif soundChoice == "Angry" then
                args = {
                    [1] = 0,
                    [2] = "Angry",
                    [3] = {
                        [1] = 133298356987645,
                        [3] = "\240\159\152\161"
                    }
                }
            elseif soundChoice == "Rich" then
                args = {
                    [1] = 0,
                    [2] = "Rich",
                    [3] = {
                        [1] = 130092168329014,
                        [3] = "\240\159\164\145",
                        [4] = {
                            [1] = 1600534477,
                            [2] = 1294179147,
                            [3] = 13291932395,
                            [4] = 15753006397
                        }
                    }
                }
            elseif soundChoice == "Skibidi" then
                args = {
                    [1] = 0,
                    [2] = "Skibidi",
                    [3] = {
                        [1] = 132608481803944,
                        [3] = "\240\159\154\189"
                    }
                }
            elseif soundChoice == "Winner" then
                args = {
                    [1] = 1.4333332777023315,
                    [2] = "Winner",
                    [3] = {
                        [1] = 124437927250586,
                        [3] = "\240\159\143\134"
                    }
                }
            end

            local success, err = pcall(function()
                rs.Packages._Index["sleitnick_net@0.2.0"].net["RF/PlayEmote"]:InvokeServer(unpack(args))
            end)

            if not success then
                warn("Failed to play emote:", err)
            end

            task.wait(0.00000000000001)
        end
    end)
end)
