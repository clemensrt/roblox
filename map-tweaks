repeat task.wait() until _G.WindUI and _G.Tabs and _G.Functions

local WindUI = _G.WindUI
local Tabs   = _G.Tabs

local function applyBridgeAppearance(part)
    part.Material = Enum.Material.Slate
    part.Color = Color3.fromRGB(140, 140, 140)

    local texture = Instance.new("Texture")
    texture.Texture = "rbxassetid://6140293213"
    texture.Transparency = 0.55
    texture.StudsPerTileU = 6
    texture.StudsPerTileV = 6
    texture.Face = Enum.NormalId.Top
    texture.Parent = part
end


local function createPlatform(startPos, endPos, thickness)
    thickness = thickness or 1

    local sizeX = math.abs(endPos.X - startPos.X)
    local sizeZ = math.abs(endPos.Z - startPos.Z)

    local part = Instance.new("Part")
    part.Size = Vector3.new(sizeX, thickness, sizeZ)
    part.Position = Vector3.new(
        math.min(startPos.X, endPos.X) + sizeX / 2,
        startPos.Y,
        math.min(startPos.Z, endPos.Z) + sizeZ / 2
    )

    part.Anchored = true
    part.CanCollide = true
    part.Name = "BridgePlatform"
    part.Parent = workspace

    applyBridgeAppearance(part)
    return part
end

local function createRamp(startPos, endPos, width, thickness)
    width = width or 20
    thickness = thickness or 1

    local mid    = (startPos + endPos) / 2
    local diff   = endPos - startPos
    local length = diff.Magnitude

    local part = Instance.new("Part")
    part.Size = Vector3.new(width, thickness, length)
    part.CFrame = CFrame.lookAt(mid, endPos)
    part.Anchored = true
    part.CanCollide = true
    part.Name = "BridgeRamp"
    part.Parent = workspace

    applyBridgeAppearance(part)
    return part
end

-- bridges

local allBridges   = {}
local bridgesOn    = false
local bridgesFirst = true

local function enableAllBridges()
    if bridgesOn then return end


    allBridges[#allBridges+1] = createRamp(
        Vector3.new(1300, 20.5, -3204),
        Vector3.new(987, -10.5, -2678),
        20, 1
    )


    allBridges[#allBridges+1] = createPlatform(
        Vector3.new(300, -5, -80),
        Vector3.new(500, -5, -60),
        1
    )


    allBridges[#allBridges+1] = createPlatform(
        Vector3.new(273, -1, -700),
        Vector3.new(522, -6, -651),
        1
    )


    allBridges[#allBridges+1] = createPlatform(
        Vector3.new(270, -1.5, -1822),
        Vector3.new(522, -1.5, -1764),
        1
    )

    WindUI:Notify({
        Title   = "Bridges",
        Content = "All bridges enabled.",
        Duration = 3
    })

    bridgesOn = true
end

local function disableAllBridges()
    for _, part in ipairs(allBridges) do
        if part and part.Parent then
            part:Destroy()
        end
    end
    table.clear(allBridges)

    if not bridgesFirst then
        WindUI:Notify({
            Title   = "Bridges",
            Content = "All bridges disabled.",
            Duration = 3
        })
    end

    bridgesOn = false
end

-- parking garage ramps

local garageRamps      = {}
local garageRampsOn    = false
local garageRampsFirst = true

local function enableGarageRamps()
    if garageRampsOn then return end

    -- city
    garageRamps[#garageRamps + 1] = createRamp(
        Vector3.new(-305, 53, 280),
        Vector3.new(-305, 48, 248),
        18,
        1
    )

    -- springfield
    garageRamps[#garageRamps + 1] = createRamp(
        Vector3.new(2687, 16.5, -2230),
        Vector3.new(2712, 12.5, -2230),
        18,
        1
    )

    WindUI:Notify({
        Title   = "Parking Garage Ramps",
        Content = "Garage ramps enabled.",
        Duration = 3
    })

    garageRampsOn = true
end

local function disableGarageRamps()
    for _, p in ipairs(garageRamps) do
        if p and p.Parent then
            p:Destroy()
        end
    end
    table.clear(garageRamps)

    if not garageRampsFirst then
        WindUI:Notify({
            Title   = "Parking Garage Ramps",
            Content = "Garage ramps disabled.",
            Duration = 3
        })
    end

    garageRampsOn = false
end

-- collision toggles

local dumpOriginal = {}
local dumpOn       = false
local dumpFirst    = true

local function setDumpCollision(disable)
    local dumpFolder = workspace:FindFirstChild("Dump")
    if not dumpFolder then
        WindUI:Notify({
            Title   = "Dump",
            Content = "Dump folder not found in Workspace.",
            Duration = 3
        })
        return
    end

    if disable then
        if dumpOn then return end

        dumpOriginal = {}
        local count = 0

        for _, inst in ipairs(dumpFolder:GetDescendants()) do
            if inst:IsA("BasePart") then
                dumpOriginal[inst] = {
                    CanCollide = inst.CanCollide,
                    CanTouch   = inst.CanTouch,
                    CanQuery   = inst.CanQuery,
                }
                inst.CanCollide = false
                inst.CanTouch   = false
                inst.CanQuery   = false
                count += 1
            end
        end

        dumpOn = true

        WindUI:Notify({
            Title   = "Dump",
            Content = "Disabled collision for "..tostring(count).." parts in Dump.",
            Duration = 4
        })
    else
        if not dumpOn then return end

        local restored = 0
        for part, data in pairs(dumpOriginal) do
            if part and part.Parent then
                part.CanCollide = data.CanCollide
                part.CanTouch   = data.CanTouch
                part.CanQuery   = data.CanQuery
                restored += 1
            end
        end

        dumpOriginal = {}
        dumpOn = false

        if not dumpFirst then
            WindUI:Notify({
                Title   = "Dump",
                Content = "Restored collision for "..tostring(restored).." parts.",
                Duration = 4
            })
        end
    end
end

-- fence collisions

local fenceOriginal = {}
local fenceOn       = false
local fenceFirst    = true

local function matchesFenceName(name)
    name = string.lower(name)

    local keywords = {
        "fence",
        "metal fence",
        "gatedoor",
        "fddoor",
    }

    for _, kw in ipairs(keywords) do
        if string.find(name, kw, 1, true) then
            return true
        end
    end
    return false
end

local function setFenceCollision(disable)
    if disable then
        if fenceOn then return end

        fenceOriginal = {}
        local count = 0

        for _, inst in ipairs(workspace:GetDescendants()) do
            if inst:IsA("Model") and matchesFenceName(inst.Name) then
                for _, part in ipairs(inst:GetDescendants()) do
                    if part:IsA("BasePart") then
                        fenceOriginal[part] = {
                            CanCollide = part.CanCollide,
                            CanTouch   = part.CanTouch,
                            CanQuery   = part.CanQuery,
                        }
                        part.CanCollide = false
                        part.CanTouch   = false
                        part.CanQuery   = false
                        count += 1
                    end
                end
            elseif inst:IsA("BasePart") and matchesFenceName(inst.Name) then
                fenceOriginal[inst] = {
                    CanCollide = inst.CanCollide,
                    CanTouch   = inst.CanTouch,
                    CanQuery   = inst.CanQuery,
                }
                inst.CanCollide = false
                inst.CanTouch   = false
                inst.CanQuery   = false
                count += 1
            end
        end

        fenceOn = true

        WindUI:Notify({
            Title   = "Fence Tools",
            Content = "Disabled collision for "..tostring(count).." fence/gate parts.",
            Duration = 4
        })

    else
        if not fenceOn then return end

        local restored = 0
        for part, data in pairs(fenceOriginal) do
            if part and part.Parent then
                part.CanCollide = data.CanCollide
                part.CanTouch   = data.CanTouch
                part.CanQuery   = data.CanQuery
                restored += 1
            end
        end

        fenceOriginal = {}
        fenceOn = false

        if not fenceFirst then
            WindUI:Notify({
                Title   = "Fence Tools",
                Content = "Restored collision for "..tostring(restored).." fence/gate parts.",
                Duration = 4
            })
        end
    end
end




Tabs.Visuals:Section({
    Title = "Map Tweaks",
    TextSize = 16,
})

Tabs.Visuals:Toggle({
    Title   = "Bridges",
    Desc    = "Enable/disable customs bridges along the river (Some require to toggle off dump collision)",
    Default = false,
    Callback = function(state)
        if state then
            enableAllBridges()
        else
            disableAllBridges()
        end
        bridgesFirst = false
    end
})

Tabs.Visuals:Toggle({
    Title   = "Parking Garage Ramps",
    Desc    = "Enable/disable ramps at both parking garages, to jump off of the top layer",
    Default = false,
    Callback = function(state)
        if state then
            enableGarageRamps()
        else
            disableGarageRamps()
        end
        garageRampsFirst = false
    end
})


Tabs.Visuals:Section({
    Title = "Collision",
    TextSize = 16,
})

Tabs.Visuals:Toggle({
    Title   = "Remove Dump Collision",
    Desc    = "Toggle collision for useless Parts like Benches",
    Default = false,
    Callback = function(state)
        setDumpCollision(state)
        dumpFirst = false
    end
})

Tabs.Visuals:Toggle({
    Title   = "Remove Fence Collision",
    Desc    = "Toggle collision for fences and gates",
    Default = false,
    Callback = function(state)
        setFenceCollision(state)
        fenceFirst = false
    end
})
