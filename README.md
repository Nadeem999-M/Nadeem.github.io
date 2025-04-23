# Nadeem.github.io
% Read CSV data into a table
data = readtable('Eppler_data.csv');

% Request user inputs (empty input skips filtering)
reynoldsInput = input('Enter Reynolds number (or press Enter to skip): ', 's');
velocityInput = input('Enter free stream velocity (or press Enter to skip): ', 's');
angleInput = input('Enter angle of attack (in degrees, or press Enter to skip): ', 's');

% Convert inputs to numeric values (if provided)
reynolds = str2double(reynoldsInput);
velocity = str2double(velocityInput);
angle = str2double(angleInput);

% Apply filters based on user inputs
filteredData = data;
if ~isempty(reynoldsInput)
    filteredData = filteredData(filteredData.ReynoldsNumber == reynolds, :);
end
if ~isempty(velocityInput)
    filteredData = filteredData(filteredData.FreeStreamVelocity == velocity, :);
end
if ~isempty(angleInput)
    filteredData = filteredData(filteredData.AngleOfAttack == angle, :);
end

% Check for valid results
if height(filteredData) == 0
    error('No airfoils match the specified parameters.');
end

% Find maximum CL/CD ratio
[bestRatio, idx] = max(filteredData.cl_cd);
bestAirfoil = filteredData(idx, :);

% Display results
fprintf('\nOptimal Airfoil: %s\n', bestAirfoil.AirfoilName);
fprintf('CL/CD: %.2f\n', bestRatio);
fprintf('CL: %.4f, CD: %.4f\n', bestAirfoil.cl, bestAirfoil.cd);

% Optional airfoil plotting (requires .dat files)
try
    airfoilCoords = readmatrix(sprintf('%s.dat', bestAirfoil.AirfoilName));
    plot(airfoilCoords(:,1), airfoilCoords(:,2), 'LineWidth', 1.5);
    title(sprintf('%s Airfoil Profile', bestAirfoil.AirfoilName));
    axis equal;
    xlabel('Chordwise Position (x/c)');
    ylabel('Thickness (y/c)');
catch
    warning('Airfoil coordinate file not found - plotting skipped.');
end
