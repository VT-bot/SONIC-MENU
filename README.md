-- Criando a GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local OpenButton = Instance.new("TextButton")
local FunctionsFrame = Instance.new("Frame")

-- Variáveis para arrastar a interface
local Dragging = false
local DragInput
local DragStart
local StartPos

-- Variáveis globais de estado
_G.noClipActive = false
_G.balaMagicaActive = false
_G.globalSpeed = 50  -- Velocidade padrão (entre 10 e 500)

-- Configurações do menu
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 120, 0, 40)
MainFrame.Position = UDim2.new(0, 20, 0, 20)
MainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Fundo Azul
MainFrame.Active = true  -- Permite input
MainFrame.Selectable = true
MainFrame.Parent = ScreenGui

OpenButton.Name = "OpenButton"
OpenButton.Size = UDim2.new(1, 0, 1, 0)
OpenButton.Text = "Sonic Store"
OpenButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.Parent = MainFrame

-- Funções do menu
FunctionsFrame.Name = "FunctionsFrame"
FunctionsFrame.Size = UDim2.new(0, 120, 0, 380)
FunctionsFrame.Position = UDim2.new(0, 0, 0, 40)
FunctionsFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 255)
FunctionsFrame.Visible = false
FunctionsFrame.Parent = MainFrame

-- Abre ou fecha o menu
OpenButton.MouseButton1Click:Connect(function()
	FunctionsFrame.Visible = not FunctionsFrame.Visible
end)

-- Permite mover o painel (MainFrame)
local UserInputService = game:GetService("UserInputService")
MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		Dragging = true
		DragStart = input.Position
		StartPos = MainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				Dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement then
		DragInput = input
	end
end)

game:GetService("RunService").Heartbeat:Connect(function()
	if Dragging and DragInput then
		local Delta = DragInput.Position - DragStart
		MainFrame.Position = UDim2.new(
			StartPos.X.Scale, StartPos.X.Offset + Delta.X,
			StartPos.Y.Scale, StartPos.Y.Offset + Delta.Y
		)
	end
end)

-- Função para criar botões com fundo azul
local function createButton(text, position, callback)
	local button = Instance.new("TextButton")
	button.Name = text.."Button"
	button.Size = UDim2.new(1, 0, 0, 30)
	button.Position = UDim2.new(0, 0, 0, position)
	button.Text = text .. " [OFF]"
	button.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Botão Azul
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.Parent = FunctionsFrame

	local state = false
	button.MouseButton1Click:Connect(function()
		state = not state
		button.Text = text .. (state and " [ON]" or " [OFF]")
		callback(state)
	end)
end

-- Botão de fechar a interface
local function createCloseButton()
	local closeButton = Instance.new("TextButton")
	closeButton.Name = "CloseButton"
	closeButton.Size = UDim2.new(0, 30, 0, 30)
	closeButton.Position = UDim2.new(1, -30, 0, 0)
	closeButton.Text = "X"
	closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Vermelho
	closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
	closeButton.Parent = MainFrame

	closeButton.MouseButton1Click:Connect(function()
		ScreenGui:Destroy()
	end)
end

createCloseButton()

--------------------------------------------------
-- FUNÇÕES

-- Invisibilidade
createButton("Invisibilidade", 0, function(state)
	local player = game.Players.LocalPlayer
	if player and player.Character then
		for _, part in ipairs(player.Character:GetChildren()) do
			if part:IsA("BasePart") then
				part.Transparency = state and 1 or 0
			end
		end
	end
end)

-- Voar (usa _G.globalSpeed)
local adminFlightConnection
createButton("Voar", 30, function(state)
	local player = game.Players.LocalPlayer
	local character = player.Character
	if character then
		local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
		if humanoidRootPart then
			local RunService = game:GetService("RunService")
			if state then
				local bodyGyro = Instance.new("BodyGyro")
				local bodyVelocity = Instance.new("BodyVelocity")
				bodyGyro.Name = "AdminFlightGyro"
				bodyVelocity.Name = "AdminFlightVel"
				bodyGyro.P = 9e4
				bodyGyro.MaxTorque = Vector3.new(9e4, 9e4, 9e4)
				bodyGyro.CFrame = workspace.CurrentCamera.CFrame
				bodyGyro.Parent = humanoidRootPart

				bodyVelocity.MaxForce = Vector3.new(9e4, 9e4, 9e4)
				bodyVelocity.Parent = humanoidRootPart

				adminFlightConnection = RunService.Heartbeat:Connect(function()
					local direction = Vector3.new(0, 0, 0)
					if UserInputService:IsKeyDown(Enum.KeyCode.W) then
						direction = direction + workspace.CurrentCamera.CFrame.LookVector
					end
					if UserInputService:IsKeyDown(Enum.KeyCode.S) then
						direction = direction - workspace.CurrentCamera.CFrame.LookVector
					end
					if UserInputService:IsKeyDown(Enum.KeyCode.A) then
						direction = direction - workspace.CurrentCamera.CFrame.RightVector
					end
					if UserInputService:IsKeyDown(Enum.KeyCode.D) then
						direction = direction + workspace.CurrentCamera.CFrame.RightVector
					end
					if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
						direction = direction + Vector3.new(0, 1, 0)
					end
					if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
						direction = direction - Vector3.new(0, 1, 0)
					end

					local gamepadInputs = UserInputService:GetGamepadState(Enum.UserInputType.Gamepad1)
					for _, input in ipairs(gamepadInputs) do
						if input.KeyCode == Enum.KeyCode.Thumbstick1 then
							local stick = input.Position
							direction = direction + (workspace.CurrentCamera.CFrame.RightVector * stick.X)
							direction = direction + (workspace.CurrentCamera.CFrame.LookVector * stick.Y)
						end
					end

					if direction.Magnitude > 0 then
						bodyVelocity.Velocity = direction.Unit * _G.globalSpeed
					else
						bodyVelocity.Velocity = Vector3.new(0, 0, 0)
					end

					bodyGyro.CFrame = workspace.CurrentCamera.CFrame
				end)
			else
				if humanoidRootPart:FindFirstChild("AdminFlightGyro") then
					humanoidRootPart.AdminFlightGyro:Destroy()
				end
				if humanoidRootPart:FindFirstChild("AdminFlightVel") then
					humanoidRootPart.AdminFlightVel:Destroy()
				end
				if adminFlightConnection then
					adminFlightConnection:Disconnect()
					adminFlightConnection = nil
				end
			end
		end
	end
end)

-- ESP
createButton("ESP Pessoa", 60, function(state)
	for _, player in pairs(game.Players:GetPlayers()) do
		if player ~= game.Players.LocalPlayer and player.Character then
			if state then
				local highlight = Instance.new("Highlight")
				highlight.Name = "ESPHighlight"
				highlight.FillColor = Color3.new(1, 0, 0)
				highlight.Parent = player.Character
			else
				if player.Character then
					for _, v in pairs(player.Character:GetChildren()) do
						if v:IsA("Highlight") and v.Name == "ESPHighlight" then
							v:Destroy()
						end
					end
				end
			end
		end
	end
end)

-- No Clip
createButton("No Clip", 90, function(state)
	local player = game.Players.LocalPlayer
	local character = player.Character
	if character then
		_G.noClipActive = state
		for _, part in ipairs(character:GetChildren()) do
			if part:IsA("BasePart") then
				part.CanCollide = not state
			end
		end
	end
end)

-- Deus
createButton("Deus", 120, function(state)
	local player = game.Players.LocalPlayer
	local character = player.Character
	if character then
		local humanoid = character:FindFirstChildOfClass("Humanoid")
		if humanoid then
			if state then
				humanoid.Health = humanoid.MaxHealth
				humanoid.Died:Connect(function() return true end)
			else
				humanoid.Health = humanoid.MaxHealth
				humanoid.Died:Disconnect()
			end
		end
	end
end)

-- Tag ADM (Chat e Acima da Cabeça)
createButton("Tag ADM", 150, function(state)
	local player = game.Players.LocalPlayer
	if state then
		game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", {
			Text = "[ADM] " .. player.Name,
			Color = Color3.fromRGB(255, 0, 0),
			Font = Enum.Font.SourceSansBold,
			TextSize = 20
		})

		local tag = Instance.new("BillboardGui")
		tag.Adornee = player.Character:WaitForChild("Head")
		tag.Name = "ADMTag"
		tag.Size = UDim2.new(0, 100, 0, 30)
		tag.StudsOffset = Vector3.new(0, 2, 0)
		tag.Parent = player.Character:WaitForChild("Head")

		local label = Instance.new("TextLabel")
		label.Size = UDim2.new(1, 0, 1, 0)
		label.Text = "[ADM]"
		label.TextColor3 = Color3.fromRGB(255, 0, 0)
		label.BackgroundTransparency = 1
		label.TextSize = 18
		label.Parent = tag
	else
		local head = player.Character and player.Character:FindFirstChild("Head")
		if head then
			local tag = head:FindFirstChild("ADMTag")
			if tag then
				tag:Destroy()
			end
		end
	end
end)

-- Bala Mágica (Projéteis de Tools equipadas se tornam "mágicos" e travam a mira no alvo)
createButton("Bala Magica", 180, function(state)
	_G.balaMagicaActive = state
	if state then
		_G.balaMagicaConnection = game.Workspace.ChildAdded:Connect(function(child)
			if child:IsA("BasePart") then
				local tool = child:FindFirstAncestorOfClass("Tool")
				if tool and tool.Parent == game.Players.LocalPlayer.Character then
					child.CanCollide = false
					spawn(function()
						while child and child.Parent and _G.balaMagicaActive do
							local lockRadius = _G.noClipActive and 20 or 2
							local closestTarget = nil
							local closestDist = lockRadius
							for _, pl in ipairs(game.Players:GetPlayers()) do
								if pl ~= game.Players.LocalPlayer and pl.Character and pl.Character:FindFirstChild("Head") then
									local dist = (child.Position - pl.Character.Head.Position).Magnitude
									if dist < closestDist then
										closestDist = dist
										closestTarget = pl.Character.Head
									end
								end
							end
							if closestTarget then
								child.Velocity = (closestTarget.Position - child.Position).Unit * _G.globalSpeed
							end
							wait(0.05)
						end
					end)
				end
			end
		end)
	else
		if _G.balaMagicaConnection then
			_G.balaMagicaConnection:Disconnect()
			_G.balaMagicaConnection = nil
		end
	end
end)

--------------------------------------------------
-- Função de Velocidade com Slider (Sem caixa de número)
createButton("Velocidade", 210, function(state)
	-- Se o botão for ativado, cria um slider simples
	if state then
		-- Cria o frame do slider
		local sliderFrame = Instance.new("Frame")
		sliderFrame.Name = "SliderFrame"
		sliderFrame.Size = UDim2.new(0, 100, 0, 20)
		sliderFrame.Position = UDim2.new(0, 10, 0, 240)
		sliderFrame.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
		sliderFrame.Parent = FunctionsFrame

		-- Cria o "track" (fundo do slider)
		local track = Instance.new("Frame")
		track.Name = "Track"
		track.Size = UDim2.new(1, 0, 1, 0)
		track.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
		track.Parent = sliderFrame

		-- Cria o botão do slider
		local sliderButton = Instance.new("Frame")
		sliderButton.Name = "SliderButton"
		sliderButton.Size = UDim2.new(0, 20, 1, 0)
		local initialPos = (_G.globalSpeed - 10) / (500 - 10)
		sliderButton.Position = UDim2.new(initialPos, 0, 0, 0)
		sliderButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
		sliderButton.Parent = sliderFrame

		-- Função para atualizar a velocidade e a WalkSpeed
		local function updateSpeedFromSlider(xOffset)
			local trackWidth = track.AbsoluteSize.X - sliderButton.AbsoluteSize.X
			local percent = math.clamp(xOffset / trackWidth, 0, 1)
			local newSpeed = math.floor(percent * (500 - 10) + 10)
			_G.globalSpeed = newSpeed
			local player = game.Players.LocalPlayer
			if player and player.Character then
				local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
				if humanoid then
					humanoid.WalkSpeed = newSpeed
				end
			end
		end

		-- Permite arrastar o sliderButton com mouse e teclado
		local draggingSlider = false
		sliderButton.InputBegan:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseButton1 then
				draggingSlider = true
			elseif input.UserInputType == Enum.UserInputType.Keyboard then
				if input.KeyCode == Enum.KeyCode.Left then
					local newX = sliderButton.Position.X.Offset - 5
					local trackWidth = track.AbsoluteSize.X - sliderButton.AbsoluteSize.X
					newX = math.clamp(newX, 0, trackWidth)
					sliderButton.Position = UDim2.new(0, newX, 0, 0)
					updateSpeedFromSlider(newX)
				elseif input.KeyCode == Enum.KeyCode.Right then
					local newX = sliderButton.Position.X.Offset + 5
					local trackWidth = track.AbsoluteSize.X - sliderButton.AbsoluteSize.X
					newX = math.clamp(newX, 0, trackWidth)
					sliderButton.Position = UDim2.new(0, newX, 0, 0)
					updateSpeedFromSlider(newX)
				end
			end
		end)
		sliderButton.InputEnded:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseButton1 then
				draggingSlider = false
			end
		end)
		UserInputService.InputChanged:Connect(function(input)
			if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
				local relativeX = input.Delta.X
				local newX = sliderButton.Position.X.Offset + relativeX
				local trackWidth = track.AbsoluteSize.X - sliderButton.AbsoluteSize.X
				newX = math.clamp(newX, 0, trackWidth)
				sliderButton.Position = UDim2.new(0, newX, 0, 0)
				updateSpeedFromSlider(newX)
			end
		end)
	else
		local sFrame = FunctionsFrame:FindFirstChild("SliderFrame")
		if sFrame then
			sFrame:Destroy()
		end
	end
end)
