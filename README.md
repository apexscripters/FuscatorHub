--[[
  Apex Scripters — V2 (Smart Farm + UI Fix)
  Otimizado para "Watch the number go up"
  Preparado, Minificado e Protegido para Delta Executor (Mobile)
]]
local P, RS, TS, UIS, L, Rep = game:GetService("Players"), game:GetService("RunService"), game:GetService("TweenService"), game:GetService("UserInputService"), game:GetService("Lighting"), game:GetService("ReplicatedStorage")
local lp = P.LocalPlayer
local guiParent = (gethui and gethui()) or game:GetService("CoreGui") or lp:WaitForChild("PlayerGui")

for _, v in ipairs(guiParent:GetChildren()) do if v.Name:sub(1,4) == "APEX" then v:Destroy() end end

local char, hum, root
local function refreshChar()
    char = lp.Character or lp.CharacterAdded:Wait()
    hum, root = char:WaitForChild("Humanoid", 5), char:WaitForChild("HumanoidRootPart", 5)
end
refreshChar(); lp.CharacterAdded:Connect(refreshChar)

local Rem = Rep:WaitForChild("Objects"):WaitForChild("Remotes")
local BUp, DPr = Rem:WaitForChild("Upgrades"):WaitForChild("BuyUpgrade"), Rem:WaitForChild("Prestige"):WaitForChild("DoPrestige")
local SCh, EGm = Rem:WaitForChild("Challenges"):WaitForChild("StartChallenge"), Rem:WaitForChild("Gems"):WaitForChild("EquipGem")
local CAc = Rem:WaitForChild("Achievements"):WaitForChild("ClaimAchievement")
local RRSt, RRDl = Rem:WaitForChild("Runes"):WaitForChild("ReplicateRuneStats"), Rem:WaitForChild("Runes"):WaitForChild("ReplicateRunesDelta")
local RAch, RMul = Rem:WaitForChild("Achievements"):WaitForChild("ReplicateAchievementProgress"), Rem:WaitForChild("Replication"):WaitForChild("ReplicateMultiplier")

local Tog = {ANum=false, AReb=false, ARebU=false, AAsc=false, AAscU=false, ATra=false, ATraU=false, AOvc=false, AOvcU=false, AGem=false, AAch=false}
local Cfg = {FarmSpeed=0.05, PrestigeDelay=15}
local tReb, tAsc, tTra, tOvc = tick(), tick(), tick(), tick()
local Placas = {"NumMulti_002", "NumMulti_003", "NumMulti_005"}

task.spawn(function()
    while true do
        task.wait(Cfg.FarmSpeed)
        if Tog.ANum then for _, p in ipairs(Placas) do pcall(function() BUp:FireServer(p) end) end end
        if Tog.ARebU then pcall(function() BUp:FireServer("RebirthNumberMulti"); BUp:FireServer("RebirthWalkspeedAdd") end) end
        if Tog.AAscU then pcall(function() BUp:FireServer("AscensionNumberMulti") end) end
        if Tog.ATraU then pcall(function() BUp:FireServer("TranscensionTeleportUnlock") end) end
        if Tog.AOvcU then pcall(function() BUp:FireServer("OverclockNumberMulti") end) end
        if Tog.AGem then pcall(function() EGm:FireServer("Number", 1, false) end) end
        if Tog.AAch then pcall(function() CAc:FireServer("TotalRunesRolled") end) end

        local cT = tick()
        if Tog.AReb and (cT - tReb) >= Cfg.PrestigeDelay then pcall(function() DPr:FireServer("Rebirth") end); tReb = cT end
        if Tog.AAsc and (cT - tAsc) >= (Cfg.PrestigeDelay * 2) then pcall(function() DPr:FireServer("Ascension") end); tAsc = cT end
        if Tog.ATra and (cT - tTra) >= (Cfg.PrestigeDelay * 3) then pcall(function() DPr:FireServer("Transcension") end); tTra = cT end
        if Tog.AOvc and (cT - tOvc) >= (Cfg.PrestigeDelay * 4) then pcall(function() DPr:FireServer("Overclock") end); tOvc = cT end
    end
end)

local function TP(x,y,z) if root then root.CFrame = CFrame.new(x,y,z) end end

local hideP = false
RS.RenderStepped:Connect(function()
    if hideP then for _, p in ipairs(P:GetPlayers()) do if p ~= lp and p.Character then for _, v in ipairs(p.Character:GetDescendants()) do if v:IsA("BasePart") or v:IsA("Decal") then v.Transparency = 1 end end end end end
end)

local function AntiLag(s)
    if s then
        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and not (v.Parent and v.Parent:FindFirstChild("Humanoid")) then v.Material = Enum.Material.SmoothPlastic; v.Reflectance = 0
            elseif v:IsA("Decal") or v:IsA("Texture") then v.Transparency = 1 end
        end
        L.GlobalShadows = false
    end
end

local function new(c, p, pa) local o = Instance.new(c); for k, v in pairs(p or {}) do o[k] = v end; if pa then o.Parent = pa end; return o end
local function rnd(p, r) local c = Instance.new("UICorner"); c.CornerRadius = UDim.new(0, r or 8); c.Parent = p end
local function tw(o, p, t) TS:Create(o, TweenInfo.new(t or .15, Enum.EasingStyle.Quad), p):Play() end

local hue = 0; RS.Heartbeat:Connect(function(dt) hue = (hue + dt * 0.4) % 1 end)
local rList = {}
local function rainbow(l, o) o = o or math.random(); rList[#rList + 1] = {lbl = l, off = o} end
RS.Heartbeat:Connect(function() for i = #rList, 1, -1 do local r = rList[i]; if r.lbl and r.lbl.Parent then r.lbl.TextColor3 = Color3.fromHSV((hue + r.off) % 1, 0.85, 1) else table.remove(rList, i) end end end)

local function draggable(w, h)
    local dT, dS, sP = false, nil, nil
    h.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dT = true; dS = i.Position; sP = w.Position end end)
    UIS.InputChanged:Connect(function(i) if (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) and dT then local d = i.Position - dS; w.Position = UDim2.new(sP.X.Scale, sP.X.Offset + d.X, sP.Y.Scale, sP.Y.Offset + d.Y) end end)
    h.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then dT = false end end)
end

local HCK = "rbxassetid://14578474118"
local function makeToggle(acc, onOp)
    local sg = new("ScreenGui", {Name="APEX_tog", ResetOnSpawn=false, ZIndexBehavior=Enum.ZIndexBehavior.Sibling}, guiParent)
    local btn = new("ImageButton", {Size=UDim2.new(0,52,0,52), Position=UDim2.new(0,16,0.5,-26), BackgroundColor3=Color3.fromRGB(10,10,16), Image=HCK, ImageTransparency=0.25, ScaleType=Enum.ScaleType.Crop, ZIndex=20, Parent=sg})
    rnd(btn, 14); new("UIStroke", {Color=acc, Thickness=2}, btn)
    local ico = new("TextLabel", {Size=UDim2.new(1,0,1,0), BackgroundTransparency=1, Text="⚡", TextSize=22, Font=Enum.Font.GothamBold, ZIndex=21, Parent=btn}); rainbow(ico); draggable(btn, btn)
    btn.MouseButton1Click:Connect(function() sg:Destroy(); onOp() end); return sg
end

local function buildLib(cfg)
    local W, H = 340, 420; local acc, bg = cfg.accent, cfg.bg
    local sg = new("ScreenGui", {Name="APEX_"..cfg.name:gsub("[^%w]",""), ResetOnSpawn=false, ZIndexBehavior=Enum.ZIndexBehavior.Sibling}, guiParent)
    local win = new("Frame", {Size=UDim2.new(0,W,0,0), Position=UDim2.new(0.5,-W/2, 0.5,-H/2), BackgroundColor3=bg, BorderSizePixel=0, ZIndex=5, ClipsDescendants=true, Parent=sg})
    rnd(win, 12); new("UIStroke", {Color=acc, Thickness=1.5}, win)
    new("ImageLabel", {Size=UDim2.new(1,0,1,0), BackgroundTransparency=1, Image=HCK, ImageTransparency=0.92, ScaleType=Enum.ScaleType.Crop, ZIndex=5, Parent=win})
    
    local hdr = new("Frame", {Size=UDim2.new(1,0,0,42), BackgroundColor3=Color3.fromRGB(math.floor(bg.R*255*0.6)/255, math.floor(bg.G*255*0.6)/255, math.floor(bg.B*255*0.6)/255), BorderSizePixel=0, ZIndex=6, Parent=win})
    rnd(hdr, 12); new("Frame",{Size=UDim2.new(1,0,0,12),Position=UDim2.new(0,0,1,-12), BackgroundColor3=hdr.BackgroundColor3,BorderSizePixel=0,ZIndex=6,Parent=hdr}); new("Frame",{Size=UDim2.new(1,0,0,2),Position=UDim2.new(0,0,1,-2), BackgroundColor3=acc,BorderSizePixel=0,ZIndex=7,Parent=hdr})
    local icof = new("Frame",{Size=UDim2.new(0,26,0,26),Position=UDim2.new(0,8,0.5,-13), BackgroundColor3=acc,ZIndex=7,Parent=hdr}); rnd(icof,7)
    new("TextLabel",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1, Text=cfg.icon or "✦",TextSize=13,Font=Enum.Font.GothamBold, TextColor3=Color3.fromRGB(255,255,255),ZIndex=8,Parent=icof})
    local titLbl = new("TextLabel",{Size=UDim2.new(1,-100,1,0),Position=UDim2.new(0,40,0,0), BackgroundTransparency=1,Text=cfg.name, TextSize=12,Font=Enum.Font.GothamBold, TextXAlignment=Enum.TextXAlignment.Left,ZIndex=7,Parent=hdr}); rainbow(titLbl)
    
    local minB = new("TextButton",{Size=UDim2.new(0,22,0,22),Position=UDim2.new(1,-52,0.5,-11), BackgroundColor3=Color3.fromRGB(50,52,70), Text="—",TextSize=10,Font=Enum.Font.GothamBold, TextColor3=Color3.fromRGB(200,200,220),ZIndex=8,Parent=hdr}); rnd(minB,5)
    local clsB = new("TextButton",{Size=UDim2.new(0,22,0,22),Position=UDim2.new(1,-26,0.5,-11), BackgroundColor3=Color3.fromRGB(200,45,45), Text="✕",TextSize=10,Font=Enum.Font.GothamBold, TextColor3=Color3.fromRGB(255,255,255),ZIndex=8,Parent=hdr}); rnd(clsB,5)
    
    local tbBar = new("Frame",{Size=UDim2.new(0,75,1,-44),Position=UDim2.new(0,0,0,44), BackgroundColor3=Color3.fromRGB(math.floor(bg.R*255*0.75)/255, math.floor(bg.G*255*0.75)/255, math.floor(bg.B*255*0.75)/255), BorderSizePixel=0,ZIndex=6,Parent=win})
    new("UIListLayout",{Padding=UDim.new(0,4),SortOrder=Enum.SortOrder.LayoutOrder},tbBar); new("UIPadding",{PaddingTop=UDim.new(0,8),PaddingLeft=UDim.new(0,6),PaddingRight=UDim.new(0,6)},tbBar)
    new("Frame",{Size=UDim2.new(0,1,1,-44),Position=UDim2.new(0,75,0,44), BackgroundColor3=acc,BackgroundTransparency=0.6,BorderSizePixel=0,ZIndex=6,Parent=win})
    
    local cnt = new("ScrollingFrame",{Size=UDim2.new(1,-77,1,-46),Position=UDim2.new(0,77,0,46), BackgroundTransparency=1,BorderSizePixel=0, ScrollBarThickness=2,ScrollBarImageColor3=acc, CanvasSize=UDim2.new(0,0,0,0),AutomaticCanvasSize=Enum.AutomaticSize.Y, ZIndex=6,Parent=win})
    new("UIListLayout",{Padding=UDim.new(0,5),SortOrder=Enum.SortOrder.LayoutOrder},cnt); new("UIPadding",{PaddingTop=UDim.new(0,8),PaddingBottom=UDim.new(0,8), PaddingLeft=UDim.new(0,6),PaddingRight=UDim.new(0,6)},cnt)
    
    draggable(win, hdr); tw(win,{Size=UDim2.new(0,W,0,H)},0.25)
    
    local cBg = new("Frame", {Size=UDim2.new(1,0,1,0), BackgroundColor3=Color3.fromRGB(0,0,0), BackgroundTransparency=0.4, ZIndex=50, Visible=false, Parent=win})
    local cBx = new("Frame", {Size=UDim2.new(0,220,0,110), Position=UDim2.new(0.5,-110,0.5,-55), BackgroundColor3=bg, ZIndex=51, Parent=cBg}); rnd(cBx, 10); new("UIStroke", {Color=acc, Thickness=1.5}, cBx)
    new("TextLabel", {Size=UDim2.new(1,0,0,40), BackgroundTransparency=1, Text="Deseja fechar o Script?", Font=Enum.Font.GothamBold, TextSize=13, TextColor3=Color3.fromRGB(255,255,255), ZIndex=52, Parent=cBx})
    local bY = new("TextButton", {Size=UDim2.new(0,80,0,30), Position=UDim2.new(0,20,1,-45), BackgroundColor3=Color3.fromRGB(200,45,45), Text="Sim", Font=Enum.Font.GothamBold, TextSize=12, TextColor3=Color3.fromRGB(255,255,255), ZIndex=52, Parent=cBx}); rnd(bY, 5)
    local bN = new("TextButton", {Size=UDim2.new(0,80,0,30), Position=UDim2.new(1,-100,1,-45), BackgroundColor3=Color3.fromRGB(50,52,70), Text="Não", Font=Enum.Font.GothamBold, TextSize=12, TextColor3=Color3.fromRGB(255,255,255), ZIndex=52, Parent=cBx}); rnd(bN, 5)

    clsB.MouseButton1Click:Connect(function() cBg.Visible = true end); bN.MouseButton1Click:Connect(function() cBg.Visible = false end)
    bY.MouseButton1Click:Connect(function() tw(win,{Size=UDim2.new(0,W,0,0)},0.15); task.wait(0.16); sg:Destroy() end)
    minB.MouseButton1Click:Connect(function() tw(win,{Size=UDim2.new(0,W,0,0)},0.15); task.wait(0.16); sg.Enabled=false; makeToggle(acc, function() sg.Enabled=true; win.Size=UDim2.new(0,W,0,0); tw(win,{Size=UDim2.new(0,W,0,H)},0.22) end) end)
    
    local pgs, tBs, aTab = {}, {}, nil
    local function selT(n) for k, p in pairs(pgs) do p.Visible=(k==n) end; for k, b in pairs(tBs) do tw(b,{BackgroundColor3=k==n and acc or Color3.fromRGB(30,32,48), BackgroundTransparency=k==n and 0 or 0.5}); b.TextColor3=k==n and Color3.fromRGB(255,255,255) or Color3.fromRGB(140,140,170) end; aTab=n end
    
    local api = {}
    function api:addTab(n, ic)
        local tb = new("TextButton",{Size=UDim2.new(1,0,0,52), BackgroundColor3=Color3.fromRGB(30,32,48), BackgroundTransparency=0.5, Text="",AutoButtonColor=false, ZIndex=7,Parent=tbBar}); rnd(tb,8)
        new("TextLabel",{Size=UDim2.new(1,0,0,22),Position=UDim2.new(0,0,0,6), BackgroundTransparency=1,Text=ic or "●",TextSize=16, Font=Enum.Font.GothamBold,TextColor3=Color3.fromRGB(200,200,220), ZIndex=8,Parent=tb})
        local nl = new("TextLabel",{Size=UDim2.new(1,-2,0,14),Position=UDim2.new(0,1,0,28), BackgroundTransparency=1,Text=n,TextSize=9, Font=Enum.Font.GothamBold,TextColor3=Color3.fromRGB(140,140,170), ZIndex=8,Parent=tb}); rainbow(nl)
        local p = new("Frame",{Size=UDim2.new(1,0,0,0),AutomaticSize=Enum.AutomaticSize.Y, BackgroundTransparency=1,Visible=false,ZIndex=6,Parent=cnt}); new("UIListLayout",{Padding=UDim.new(0,5),SortOrder=Enum.SortOrder.LayoutOrder},p)
        pgs[n] = p; tBs[n] = tb; if not aTab then selT(n) end; tb.MouseButton1Click:Connect(function() selT(n) end)
        
        local papi = {_o=0}
        function papi:section(txt) self._o+=1; local f=new("Frame",{Size=UDim2.new(1,0,0,18),LayoutOrder=self._o, BackgroundColor3=Color3.fromRGB(22,24,38),BorderSizePixel=0,ZIndex=7,Parent=p}); rnd(f,5); local sl=new("TextLabel",{Size=UDim2.new(1,-8,1,0),Position=UDim2.new(0,6,0,0), BackgroundTransparency=1,Text=txt,TextSize=10,Font=Enum.Font.GothamBold, TextXAlignment=Enum.TextXAlignment.Left,ZIndex=8,Parent=f}); rainbow(sl) end
        function papi:btn(txt, cb) self._o+=1; local f=new("Frame",{Size=UDim2.new(1,0,0,32),LayoutOrder=self._o, BackgroundColor3=Color3.fromRGB(18,20,30),BorderSizePixel=0,ZIndex=7,Parent=p}); rnd(f,7); local tl=new("TextLabel",{Size=UDim2.new(1,-60,1,0),Position=UDim2.new(0,8,0,0), BackgroundTransparency=1,Text=txt,TextSize=11,Font=Enum.Font.GothamBold, TextXAlignment=Enum.TextXAlignment.Left,ZIndex=8,Parent=f}); rainbow(tl); local b=new("TextButton",{Size=UDim2.new(0,50,0,22),Position=UDim2.new(1,-56,0.5,-11), BackgroundColor3=acc,Text="Exec",TextSize=10,Font=Enum.Font.GothamBold, TextColor3=Color3.fromRGB(255,255,255),ZIndex=9,Parent=f}); rnd(b,5); b.MouseButton1Click:Connect(function() tw(b,{BackgroundTransparency=0.5}); task.delay(0.12,function() tw(b,{BackgroundTransparency=0}) end); if cb then task.spawn(pcall,cb) end end) end
        function papi:toggle(txt, def, cb) self._o+=1; local st=def or false; local f=new("Frame",{Size=UDim2.new(1,0,0,32),LayoutOrder=self._o, BackgroundColor3=Color3.fromRGB(18,20,30),BorderSizePixel=0,ZIndex=7,Parent=p}); rnd(f,7); local tl=new("TextLabel",{Size=UDim2.new(1,-56,1,0),Position=UDim2.new(0,8,0,0), BackgroundTransparency=1,Text=txt,TextSize=11,Font=Enum.Font.GothamBold, TextXAlignment=Enum.TextXAlignment.Left,ZIndex=8,Parent=f}); rainbow(tl); local tr=new("Frame",{Size=UDim2.new(0,36,0,18),Position=UDim2.new(1,-42,0.5,-9), BackgroundColor3=st and acc or Color3.fromRGB(40,42,55),ZIndex=9,Parent=f}); rnd(tr,9); local kn=new("Frame",{Size=UDim2.new(0,12,0,12), Position=st and UDim2.new(1,-15,0.5,-6) or UDim2.new(0,3,0.5,-6), BackgroundColor3=Color3.fromRGB(255,255,255),ZIndex=10,Parent=tr}); rnd(kn,6); local cl=new("TextButton",{Size=UDim2.new(1,0,1,0),BackgroundTransparency=1, Text="",ZIndex=11,Parent=f})
            cl.MouseButton1Click:Connect(function() st=not st; tw(tr,{BackgroundColor3=st and acc or Color3.fromRGB(40,42,55)}); tw(kn,{Position=st and UDim2.new(1,-15,0.5,-6) or UDim2.new(0,3,0.5,-6)}); if cb then task.spawn(pcall,cb,st) end end)
        end
        function papi:slider(txt, mn, mx, def, isF, cb) self._o+=1; local v=math.clamp(def or mn,mn,mx); local f=new("Frame",{Size=UDim2.new(1,0,0,46),LayoutOrder=self._o, BackgroundColor3=Color3.fromRGB(18,20,30),BorderSizePixel=0,ZIndex=7,Parent=p}); rnd(f,7); local tl=new("TextLabel",{Size=UDim2.new(0.6,0,0,16),Position=UDim2.new(0,8,0,5), BackgroundTransparency=1,Text=txt,TextSize=10,Font=Enum.Font.GothamBold, TextXAlignment=Enum.TextXAlignment.Left,ZIndex=8,Parent=f}); rainbow(tl); local vl=new("TextLabel",{Size=UDim2.new(0.38,0,0,16),Position=UDim2.new(0.6,0,0,5), BackgroundTransparency=1,Text=tostring(v),TextSize=10,Font=Enum.Font.GothamBold, TextColor3=acc,TextXAlignment=Enum.TextXAlignment.Right,ZIndex=8,Parent=f}); local tr=new("Frame",{Size=UDim2.new(1,-16,0,4),Position=UDim2.new(0,8,0,28), BackgroundColor3=Color3.fromRGB(35,37,50),ZIndex=8,Parent=f}); rnd(tr,2); local fi=new("Frame",{Size=UDim2.new((v-mn)/(mx-mn),0,1,0), BackgroundColor3=acc,ZIndex=9,Parent=tr}); rnd(fi,2); local kn=new("Frame",{Size=UDim2.new(0,11,0,11), Position=UDim2.new((v-mn)/(mx-mn),-5,0.5,-5), BackgroundColor3=Color3.fromRGB(255,255,255),ZIndex=10,Parent=tr}); rnd(kn,6); new("UIStroke",{Color=acc,Thickness=1.5},kn)
            local d2=false; kn.InputBegan:Connect(function(i) if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then d2=true end end); UIS.InputEnded:Connect(function(i) if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then d2=false end end)
            UIS.InputChanged:Connect(function(i) if d2 and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then local r=math.clamp((i.Position.X-tr.AbsolutePosition.X)/tr.AbsoluteSize.X,0,1); v=isF and (mn+(mx-mn)*r) or math.round(mn+(mx-mn)*r); vl.Text=isF and string.format("%.2f",v) or tostring(v); fi.Size=UDim2.new(r,0,1,0); kn.Position=UDim2.new(r,-5,0.5,-5); if cb then task.spawn(pcall,cb,v) end end end)
        end
        return papi
    end
    return api
end

local function buildContent(w)
    local t1 = w:addTab("Auto","⚡")
    t1:section("Config do Acúmulo"); t1:slider("Atraso Prestige (Segs)", 1, 120, 15, false, function(v) Cfg.PrestigeDelay = v end)
    t1:section("Farm de Números"); t1:toggle("Auto Placas", false, function(s) Tog.ANum = s end)
    t1:section("Prestígios Inteligentes"); t1:toggle("Auto Rebirth", false, function(s) Tog.AReb = s end); t1:toggle("Auto Ascensão", false, function(s) Tog.AAsc = s end); t1:toggle("Auto Transcensão", false, function(s) Tog.ATra = s end); t1:toggle("Auto EverClock", false, function(s) Tog.AOvc = s end)
    t1:section("Upgrades & Extras"); t1:toggle("Upgrades Rebirth", false, function(s) Tog.ARebU = s end); t1:toggle("Upgrades Ascensão", false, function(s) Tog.AAscU = s end); t1:toggle("Upgrades Transcensão", false, function(s) Tog.ATraU = s end); t1:toggle("Upgrades EverClock", false, function(s) Tog.AOvcU = s end); t1:toggle("Auto Equip Gemas", false, function(s) Tog.AGem = s end); t1:toggle("Auto Conquistas", false, function(s) Tog.AAch = s end)

    local t2 = w:addTab("Manual","🔨")
    t2:section("Ações e Upgrades"); t2:btn("Comprar Placas (1x)", function() BUp:FireServer("NumMulti_002") BUp:FireServer("NumMulti_003") BUp:FireServer("NumMulti_005") end); t2:btn("Rebirth", function() DPr:FireServer("Rebirth") end); t2:btn("Ascensão", function() DPr:FireServer("Ascension") end); t2:btn("Transcensão", function() DPr:FireServer("Transcension") end); t2:btn("Everclock", function() DPr:FireServer("Overclock") end)
    t2:section("Desafios"); t2:btn("Num Speedrun", function() SCh:FireServer("NumberSpeedrun") end); t2:btn("Num Debuff", function() SCh:FireServer("NumberDebuff") end); t2:btn("Roll Runes", function() SCh:FireServer("RollRunes") end); t2:btn("Roll Rare", function() SCh:FireServer("RollRareRunes") end); t2:btn("Gem Merge", function() SCh:FireServer("GemMerge") end); t2:btn("Gem Collector", function() SCh:FireServer("GemCollector") end); t2:btn("Researcher", function() SCh:FireServer("Researcher") end); t2:btn("Rune RNG", function() SCh:FireServer("RuneRNG") end)
    t2:section("Spoof (Visual/Firesignal)"); t2:btn("Spoof Runas Stats", function() firesignal(RRSt.OnClientEvent, {LuckMulti=1, RollSpeed=1, RunePowerExponent=1, RuneBulkTotal=0, Luck=1.024}) end); t2:btn("Spoof Conquistas", function() firesignal(RAch.OnClientEvent, {RuneLuck=1.065, TotalRunesRolled=65}) end); t2:btn("Spoof Runas Delta", function() firesignal(RRDl.OnClientEvent, "Number", {["2"]=1}) end); t2:btn("Spoof Multi", function() firesignal(RMul.OnClientEvent, "AllMainStatsMulti", {heptate=0, multiplicand=1, tetrate=0, sign=1, hexate=0, pentate=0, exponent=0}) end)

    local t3 = w:addTab("Locais","📍")
    t3:section("Zonas"); t3:btn("Início", function() TP(-29, 78, 1) end); t3:btn("Rebirth", function() TP(-7, 78, -107) end); t3:btn("Multi 1", function() TP(77, 78, 69) end); t3:btn("Multi 2", function() TP(120, 78, 69) end); t3:btn("Multi 3", function() TP(161, 78, 69) end); t3:btn("Reset Multis", function() TP(205, 78, -37) end); t3:btn("Runas de Num", function() TP(31, 78, -255) end); t3:btn("Ascensão", function() TP(-6, 78, -410) end); t3:btn("Desafios", function() TP(8, 78, -528) end); t3:btn("Joias", function() TP(24, 78, -647) end); t3:btn("Transcensão", function() TP(-8, 78, -779) end); t3:btn("Relógio", function() TP(-7, 78, -1411) end); t3:btn("Pesquisa", function() TP(125, 78, -193) end); t3:btn("Conquista", function() TP(126, 78, -303) end)

    local t4 = w:addTab("Configs","⚙")
    t4:section("Performance"); t4:toggle("Anti-Lag", false, function(s) AntiLag(s) end); t4:toggle("Ocultar Jogadores", false, function(s) hideP = s end)
    t4:section("Mundo"); t4:slider("Clima (Hora)", 0, 24, 14, false, function(v) L.ClockTime=v end); t4:toggle("Noite Eterna", false, function(s) L.ClockTime = s and 0 or 14 end); t4:toggle("Dia Eterno", false, function(s) L.ClockTime = s and 12 or 14 end)
end

buildContent(buildLib({name="Apex Scripters | Watch Num", accent=Color3.fromRGB(0,255,150), bg=Color3.fromRGB(10,12,20), icon="📈"}))
