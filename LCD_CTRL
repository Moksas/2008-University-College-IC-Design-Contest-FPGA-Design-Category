
module LCD_CTRL(clk,reset,datain,cmd,cmd_valid,dataout,output_valid, busy); 

input clk;
input reset;
input [7:0] datain;
input [2:0] cmd;
input cmd_valid;
reg [2:0] cmdinput;
reg [7:0] dataarray [107:0];
reg [2:0] currentstate;
reg [2:0] nextstate;
 reg [6:0] ozecount;//107=7'b1101011
 reg [4:0] oscount;//16=5'b10000
reg [6:0]temposcount;
reg [3:0] originx;
reg [3:0] originy;
output [7:0] dataout;
output output_valid;
output busy;
reg output_valid,busy;
 reg [7:0] dataout;
parameter LOAD=3'b000,ZOOM_IN=3'b001,ZOOM_FIT=3'b010,RIGHT=3'b011,LEFT=3'b100,
UP=3'b101,DOWN=3'b110,IDLE=3'b111;

always@( posedge clk or  reset)
  begin 
    if(reset)
      begin
        currentstate=IDLE;
        cmdinput=3'b111;
        nextstate=IDLE;
        busy=1'b0;
        output_valid=1'b0;
      end
    else
      begin
      currentstate=nextstate;
  
      end      
  end

  
always@(posedge clk)
begin
  
  case(currentstate)
    LOAD:
      begin
        if(ozecount==7'b1101011)
          begin
          nextstate=ZOOM_FIT;
          oscount=5'd0;
         ozecount=7'd0;
          //busy=1'b0;
          end
        else
          begin
             busy=1'b1;
            dataarray[ozecount]=datain;
            ozecount<=ozecount+7'd1;
            // dataout<= dataarray[ozecount];
          end
      end
  ZOOM_FIT:
    begin
        if(oscount==5'd16)
          begin
            oscount=oscount;
            busy=1'b0;
             output_valid=0;
              ozecount=7'd0;
          end
        else
          begin
            busy=1'b1;
             output_valid=1;
            temposcount=(oscount>>2)<<2;
            ozecount= (temposcount<<1)+temposcount;
            dataout=dataarray[7'd13+ozecount+(oscount<<1)+oscount];
            oscount=oscount+5'd1;
           
          end    
    end  
  ZOOM_IN:
    begin
       if(oscount==5'd16)
          begin
            oscount=oscount;
            busy=1'b0;
             output_valid=0;
             ozecount=7'd0;
          end
      else
        begin
           busy=1'b1;
           temposcount=(originy-2+(oscount>>2));
           temposcount=(temposcount<<3)+(temposcount<<2);
           ozecount=temposcount+originx-2+oscount[1:0];
           dataout= dataarray[ ozecount];
           oscount=oscount+5'd1;
           output_valid=1;
        end
      
    end
  endcase
//dataout= currentstate;
end
  
  //cmd block
always @(posedge clk)
begin
  if(cmd_valid)//if cmd is valid
    begin
        cmdinput=cmd; //get cmd
    
    case(currentstate)
      IDLE:if(cmdinput==LOAD)//initail
            begin
              busy=1'b1;
              nextstate=LOAD;
              ozecount=7'b0000000;
            end
            else
              nextstate=IDLE;
      ZOOM_FIT:        //when zommfit 
            begin
              case(cmdinput)
                LOAD: nextstate=LOAD;
                ZOOM_IN:
                begin
                   busy=1'b1;
                  originx=4'd6;
                  originy=4'd5;
                  oscount=5'd0;
                  nextstate=ZOOM_IN;
               
                end
                default:
                  begin  
                     busy=1'b1;
                   nextstate=ZOOM_FIT;
                   oscount=5'd0;
                  end
              endcase
            end
        ZOOM_IN:
          begin
           case(cmdinput)
             LOAD:
              begin
                 busy=1'b1;
                  nextstate=LOAD;
                  ozecount=7'd0;
              end
            ZOOM_FIT:
              begin
                 busy=1'b1;
                  oscount=5'd0;
                nextstate=ZOOM_FIT;
              end
            UP:
              begin
                 busy=1'b1;
                if(originy>4'd2)
                  originy=originy-4'd1;
                else
                  originy=originy;
                oscount=5'd0;  
              end
            DOWN:
              begin
                 busy=1'b1;
                if(originy<4'd7)
                  originy=originy+4'd1;
                else
                  originy=originy;
                 oscount=5'd0;    
              end
            RIGHT:
              begin
                 busy=1'b1;
                if(originx<4'd10)
                  originx=originx+4'd1;
                else
                  originx=originx;
                  oscount=5'd0;    
              end
            LEFT:
              begin
                 busy=1'b1;
                if(originx>4'd2)
                  originx=originx-4'd1;
                else
                  originx=originx;
                oscount=5'd0;
              end
            default:
              begin
                nextstate=ZOOM_IN;
                 busy=1'b1;
                  oscount=5'd0;
              end
            endcase

        end
      endcase   
    end
    else
      nextstate=nextstate;

end   
endmodule 
