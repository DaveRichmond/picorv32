
CROSS=riscv32-unknown-elf-
CFLAGS=

# ---- iCE40 HX8K Breakout Board ----

hx8ksim: hx8kdemo_tb.vvp hx8kdemo_fw.hex
	vvp -N $< +firmware=hx8kdemo_fw.hex

hx8ksynsim: hx8kdemo_syn_tb.vvp hx8kdemo_fw.hex
	vvp -N $< +firmware=hx8kdemo_fw.hex

hx8kdemo.blif: hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql hx8kdemo.log -p 'synth_ice40 -top hx8kdemo -blif hx8kdemo.blif' $^

hx8kdemo_tb.vvp: hx8kdemo_tb.v hx8kdemo.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

hx8kdemo_syn_tb.vvp: hx8kdemo_tb.v hx8kdemo_syn.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

hx8kdemo_syn.v: hx8kdemo.blif
	yosys -p 'read_blif -wideports hx8kdemo.blif; write_verilog hx8kdemo_syn.v'

hx8kdemo.asc: hx8kdemo.pcf hx8kdemo.blif
	arachne-pnr -d 8k -o hx8kdemo.asc -p hx8kdemo.pcf hx8kdemo.blif

hx8kdemo.bin: hx8kdemo.asc
	icetime -d hx8k -c 12 -mtr hx8kdemo.rpt hx8kdemo.asc
	icepack hx8kdemo.asc hx8kdemo.bin

hx8kprog: hx8kdemo.bin hx8kdemo_fw.bin
	iceprog hx8kdemo.bin
	iceprog -o 1M hx8kdemo_fw.bin

hx8kprog_fw: hx8kdemo_fw.bin
	iceprog -o 1M hx8kdemo_fw.bin

hx8kdemo_sections.lds: sections.lds
	$(CROSS)cpp -P -DHX8KDEMO -o $@ $^

hx8kdemo_fw.elf: hx8kdemo_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DHX8KDEMO -march=rv32imc -Wl,-Bstatic,-T,hx8kdemo_sections.lds,--strip-debug -ffreestanding -nostdlib -o hx8kdemo_fw.elf start.s firmware.c

hx8kdemo_fw.hex: hx8kdemo_fw.elf
	$(CROSS)objcopy -O verilog hx8kdemo_fw.elf hx8kdemo_fw.hex

hx8kdemo_fw.bin: hx8kdemo_fw.elf
	$(CROSS)objcopy -O binary hx8kdemo_fw.elf hx8kdemo_fw.bin

# ---- iCE40 IceBreaker Board ----

icebsim: icebreaker_tb.vvp icebreaker_fw.hex
	vvp -N $< +firmware=icebreaker_fw.hex

icebsynsim: icebreaker_syn_tb.vvp icebreaker_fw.hex
	vvp -N $< +firmware=icebreaker_fw.hex

icebreaker.json: icebreaker.v ice40up5k_spram.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql icebreaker.log -p 'synth_ice40 -top icebreaker -json icebreaker.json' $^

icebreaker_tb.vvp: icebreaker_tb.v icebreaker.v ice40up5k_spram.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

icebreaker_syn_tb.vvp: icebreaker_tb.v icebreaker_syn.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/ice40/cells_sim.v`

icebreaker_syn.v: icebreaker.json
	yosys -p 'read_json icebreaker.json; write_verilog icebreaker_syn.v'

icebreaker.asc: icebreaker.pcf icebreaker.json
	nextpnr-ice40 --freq 13 --up5k --asc icebreaker.asc --pcf icebreaker.pcf --json icebreaker.json

icebreaker.bin: icebreaker.asc
	icetime -d up5k -c 12 -mtr icebreaker.rpt icebreaker.asc
	icepack icebreaker.asc icebreaker.bin

icebprog: icebreaker.bin icebreaker_fw.bin
	iceprog icebreaker.bin
	iceprog -o 1M icebreaker_fw.bin

icebprog_fw: icebreaker_fw.bin
	iceprog -o 1M icebreaker_fw.bin

icebreaker_sections.lds: sections.lds
	$(CROSS)cpp -P -DICEBREAKER -o $@ $^

icebreaker_fw.elf: icebreaker_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DICEBREAKER -march=rv32ic -Wl,-Bstatic,-T,icebreaker_sections.lds,--strip-debug -ffreestanding -nostdlib -o icebreaker_fw.elf start.s firmware.c

icebreaker_fw.hex: icebreaker_fw.elf
	$(CROSS)objcopy -O verilog icebreaker_fw.elf icebreaker_fw.hex

icebreaker_fw.bin: icebreaker_fw.elf
	$(CROSS)objcopy -O binary icebreaker_fw.elf icebreaker_fw.bin

# ---- Spartan 6 Test Board ----
xc6s_sim: xc6s_tb.vvp xc6s_fw.hex
	vvp -N $< +firmware=xc6s_fw.hex

xc6s_synsim: xc6s_syn_tb.vvp xc6s_fw.hex
	vvp -N $< +firmware=xc6s_fw.hex

xc6s.edif: xc6s.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql xc6s.log -p 'synth_xilinx -family xc6s -ise -top xc6s_demo; write_edif -pvector bra xc6s.edif' $^
xc6s.json: xc6s.v spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -ql xc6s.log -p 'synth_xilinx -family xc6s -ise -top xc6s_demo; write_json xc6s.json' $^

XILINX_ISE ?= ${XILINX}/bin/lin64
xc6s.bit: xc6s.edif xc6s.ucf
	"${XILINX_ISE}/ngdbuild" -uc "xc6s.ucf" -p "xc6slx9-tqg144-2" "xc6s.edif" "xc6s.ngd"
	"${XILINX_ISE}/map" -p "xc6slx9-tqg144-2" -w -mt 2 -o "xc6s_map.ncd" "xc6s.ngd"
	"${XILINX_ISE}/par" -w -mt 4 "xc6s_map.ncd" "xc6s.ncd"

	# Timing report and bitstream generation
	"${XILINX_ISE}/trce" -v -n -fastpaths "xc6s.ncd" -o "xc6s.twr" "xc6s_map.pcf"
	"${XILINX_ISE}/bitgen" -w -g Binary:Yes -g Compress -g UnusedPin:PullNone "xc6s.ncd"
	# cleanup "junk"
	rm -Rf xlnx_auto_0_xdb _xmsgs netlist.lst par_usage_statistics.html \
		usage_statistics_webtalk.html xc6s_map.* xc6s.bgn \
		xc6s_bitgen.* xc6s.bld  xc6s*.xrpt xc6s_map.* xc6s_pad.* \
		xc6s.drc xc6s.ncd xc6s.ngd xc6s.ngo xc6s.pad xc6s.par \
		xc6s.ptwx xc6s.twr xc6s.twx xc6s.unroutes xc6s.xpi xc6s_*.xml \
		webtalk.log

xc6s_tb.vvp: xc6s_tb.v xc6s.v spimemio.v simpleuart.v picosoc.v ../picorv32.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/xilinx/cells_sim.v` `yosys-config --datdir/xilinx/cells_xtra.v`

xc6s_syn_tb.vvp: xc6s_tb.v xc6s_syn.v spiflash.v
	iverilog -s testbench -o $@ $^ `yosys-config --datdir/xilinx/cells_sim.v` `yosys-config --datdir/xilinx/cells_xtra.v`

xc6s_syn.v: xc6s.json
	yosys -p 'read_json xc6s.json; write_verilog xc6s_syn.v'

xc6s_sections.lds: sections.lds
	$(CROSS)cpp -P -DXC6S -o $@ $^

xc6s_fw.elf: xc6s_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DXC6S -march=rv32imc -Wl,-Bstatic,-T,xc6s_sections.lds,--strip-debug -ffreestanding -nostdlib -o xc6s_fw.elf start.s firmware.c

xc6s_fw.hex: xc6s_fw.elf
	$(CROSS)objcopy -O verilog xc6s_fw.elf xc6s_fw.hex

xc6s_fw.bin: xc6s_fw.elf
	$(CROSS)objcopy -O binary xc6s_fw.elf xc6s_fw.bin


# ---- Testbench for SPI Flash Model ----

spiflash_tb: spiflash_tb.vvp firmware.hex
	vvp -N $<

spiflash_tb.vvp: spiflash.v spiflash_tb.v
	iverilog -s testbench -o $@ $^

# ---- ASIC Synthesis Tests ----

cmos.log: spimemio.v simpleuart.v picosoc.v ../picorv32.v
	yosys -l cmos.log -p 'synth -top picosoc; abc -g cmos2; opt -fast; stat' $^

# ---- Clean ----

clean:
	rm -f testbench.vvp testbench.vcd spiflash_tb.vvp spiflash_tb.vcd
	rm -f hx8kdemo_fw.elf hx8kdemo_fw.hex hx8kdemo_fw.bin cmos.log
	rm -f icebreaker_fw.elf icebreaker_fw.hex icebreaker_fw.bin
	rm -f hx8kdemo.blif hx8kdemo.log hx8kdemo.asc hx8kdemo.rpt hx8kdemo.bin
	rm -f hx8kdemo_syn.v hx8kdemo_syn_tb.vvp hx8kdemo_tb.vvp
	rm -f icebreaker.json icebreaker.log icebreaker.asc icebreaker.rpt icebreaker.bin
	rm -f icebreaker_syn.v icebreaker_syn_tb.vvp icebreaker_tb.vvp
	rm -f xc6s.log xc6s.edif xc6s.bin xc6s.bit xc6s.json

.PHONY: spiflash_tb clean
.PHONY: hx8kprog hx8kprog_fw hx8ksim hx8ksynsim
.PHONY: icebprog icebprog_fw icebsim icebsynsim
